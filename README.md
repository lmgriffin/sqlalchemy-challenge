# sqlalchemy-challenge

##### CODE TAKEN FROM MODULES
import numpy as np

import sqlalchemy
from sqlalchemy.ext.automap import automap_base
from sqlalchemy.orm import Session
from sqlalchemy import create_engine, func

from flask import Flask, jsonify


#################################################
# Database Setup
#################################################
engine = create_engine("sqlite:///titanic.sqlite")

# reflect an existing database into a new model
Base = automap_base()
# reflect the tables
Base.prepare(autoload_with=engine)

# Save reference to the table
Passenger = Base.classes.passenger

#################################################
# Flask Setup
#################################################
app = Flask(__name__)

#################################################
# Flask Routes
#################################################

@app.route("/")
def welcome():
    """List all available api routes."""
    return (
        f"Available Routes:<br/>"
        f"/api/v1.0/names<br/>"
        f"/api/v1.0/passengers<br/>"
        f"/api/v1.0/gender/gender<br/>"
        f"/api/v1.0/age_range/lower_age/upper_age<br/>"
    )


@app.route("/api/v1.0/names")
def names():
    # Create our session (link) from Python to the DB
    session = Session(engine)

    """Return a list of all passenger names"""
    # Query all passengers
    results = session.query(Passenger.name).all()
    
    session.close()
    
    # all_names = [list(r)[0] for r in results]

    # Convert list of tuples into normal list - flattens results
    all_names = list(np.ravel(results))

    return jsonify(all_names)

@app.route("/api/v1.0/passengers")
def passengers():
    # Create our session (link) from Python to the DB
    session = Session(engine)

    """Return a list of passenger data including the name, age, and sex of each passenger"""
    # Query all passengers
    results = session.query(Passenger.name, Passenger.age, Passenger.sex).all()

    session.close()

    # Create a dictionary from the row data and append to a list of all_passengers
    all_passengers = []
    for name, age, sex in results:
        passenger_dict = {}
        passenger_dict["name"] = name
        passenger_dict["age"] = age
        passenger_dict["sex"] = sex
        all_passengers.append(passenger_dict)

    return jsonify(all_passengers)


@app.route("/api/v1.0/gender/<gender_to_query>")
def get_gender(gender_to_query):
    # Create our session (link) from Python to the DB
    session = Session(engine)

    """Return a list of passenger data by gender"""
    # Query all passengers by gender
    results = session.query(Passenger.name, Passenger.age, Passenger.sex) \
    .filter(Passenger.sex == gender_to_query) \
    .all()

    session.close()

    # Create a dictionary from the row data and append to a list of all_passengers
    all_passengers = []
    for name, age, sex in results:
        passenger_dict = {}
        passenger_dict["name"] = name
        passenger_dict["age"] = age
        passenger_dict["sex"] = sex
        all_passengers.append(passenger_dict)

    return jsonify(all_passengers)


@app.route("/api/v1.0/age_range/<lower_age>/<upper_age>")
def get_age(lower_age, upper_age):
    # Create our session (link) from Python to the DB
    session = Session(engine)

    """Return a list of passenger data between an age range"""
    # Query all passengers by gender
    results = session.query(Passenger.name, Passenger.age, Passenger.sex) \
    .filter(Passenger.age >= lower_age) \
    .filter(Passenger.age <= upper_age) \
    .all()

    session.close()

    # Create a dictionary from the row data and append to a list of all_passengers
    all_passengers = []
    for name, age, sex in results:
        passenger_dict = {}
        passenger_dict["name"] = name
        passenger_dict["age"] = age
        passenger_dict["sex"] = sex
        all_passengers.append(passenger_dict)

    return jsonify(all_passengers)


if __name__ == '__main__':
    app.run(debug=True)

# ..................................

# Reflect Database into ORM classes
Base = automap_base()
Base.prepare(autoload_with=engine)
Base.classes.keys()

# Create an engine for the chinook.sqlite database
engine = create_engine("sqlite:///../Resources/chinook.sqlite", echo=False)

# Save a reference to the invoices table as `Invoices`
Invoices = Base.classes.invoices

# Create a database session object
session = Session(engine)

