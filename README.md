# Copi
No somos taxis ni remises, somos caritas felices 
# Proyecto Copi a un Toque: Parte 1 - Backend y Frontend

## Estructura del Proyecto

plaintext
copi-a-un-toque/
│
├── backend/
│   ├── app.py
│   ├── config.py
│   ├── models.py
│   ├── routes.py
│   └── database.py
│
├── frontend/
│   ├── App.js
│   ├── package.json
│   ├── components/
│   └── screens/
│
├── README.md
└── .gitignore


## Backend (Flask)

### `app.py`
Configuración principal de la aplicación Flask y los complementos.

python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_jwt_extended import JWTManager
from models import db
from routes import main
from config import Config

app = Flask(_name_)
app.config.from_object(Config)

db.init_app(app)
jwt = JWTManager(app)

app.register_blueprint(main)

if _name_ == "_main_":
    with app.app_context():
        db.create_all()
    app.run(debug=True)


### `config.py`
Archivo de configuración.

python
import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'your_secret_key'
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'sqlite:///app.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    JWT_SECRET_KEY = os.environ.get('JWT_SECRET_KEY') or 'your_jwt_secret_key'


### `models.py`
Definición de los modelos de la base de datos usando SQLAlchemy.

python
from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash

db = SQLAlchemy()

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)
    
    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

class Ride(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    driver_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)
    origin = db.Column(db.String(120), nullable=False)
    destination = db.Column(db.String(120), nullable=False)
    status = db.Column(db.String(20), nullable=False, default='pending')
    fare = db.Column(db.Float, nullable=False, default=0.0)
    distance = db.Column(db.Float, nullable=False, default=0.0)
    start_time = db.Column(db.DateTime, nullable=True)
    end_time = db.Column(db.DateTime, nullable=True)


### `routes.py`
Definición de rutas para la API, incluyendo autenticación y manejo de errores.

python
from flask import Blueprint, request, jsonify
from flask_jwt_extended import create_access_token, jwt_required, get_jwt_identity
from models import db, User, Ride
from datetime import datetime

main = Blueprint('main', _name_)

@main.route('/register', methods=['POST'])
def register():
    data = request.get_json()
    if User.query.filter_by(username=data['username']).first() or User.query.filter_by(email=data['email']).first():
        return jsonify({"message": "User already exists"}), 400

    new_user = User(username=data['username'], email=data['email'])
    new_user.set_password(data['password'])
    db.session.add(new_user)
    db.session.commit()
    return jsonify({"message": "User registered successfully"}), 201

@main.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    user = User.query.filter_by(username=data['username']).first()
    if user is None or not …
 Parte 1 - Backend y Frontend

## Estructura del Proyecto

```plaintext
copi-a-un-toque/
│
├── backend/
│   ├── app.py
│   ├── config.py
│   ├── models.py
│   ├── routes.py
│   └── database.py
│
├── frontend/
│   ├── App.js
│   ├── package.json
│   ├── components/
│   └── screens/
│
├── README.md
└── .gitignore
[23:09, 9/2/2024] Nacho: from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from flask_jwt_extended import JWTManager
from models import db
from routes import main
from config import Config

app = Flask(_name_)
app.config.from_object(Config)

db.init_app(app)
jwt = JWTManager(app)

app.register_blueprint(main)

if _name_ == "_main_":
    with app.app_context():
        db.create_all()
    app.run(debug=True)
[23:09, 9/2/2024] Nacho: import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'your_secret_key'
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'sqlite:///app.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    JWT_SECRET_KEY = os.environ.get('JWT_SECRET_KEY') or 'your_jwt_secret_key'
[23:09, 9/2/2024] Nacho: from flask_sqlalchemy import SQLAlchemy
from werkzeug.security import generate_password_hash, check_password_hash

db = SQLAlchemy()

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(128), nullable=False)

    def set_password(self, password):
        self.password_hash = generate_password_hash(password)
    
    def check_password(self, password):
        return check_password_hash(self.password_hash, password)

class Ride(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)
    driver_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=True)
    origin = db.Column(db.String(120), nullable=False)
    destination = db.Column(db.String(120), nullable=False)
    status = db.Column(db.String(20), nullable=False, default='pending')
    fare = db.Column(db.Float, nullable=False, default=0.0)
    distance = db.Column(db.Float, nullable=False, default=0.0)
    start_time = db.Column(db.DateTime, nullable=True)
    end_time = db.Column(db.DateTime, nullable=True)
[23:09, 9/2/2024] Nacho: from flask import Blueprint, request, jsonify
from flask_jwt_extended import create_access_token, jwt_required, get_jwt_identity
from models import db, User, Ride
from datetime import datetime

main = Blueprint('main', _name_)

@main.route('/register', methods=['POST'])
def register():
    data = request.get_json()
    if User.query.filter_by(username=data['username']).first() or User.query.filter_by(email=data['email']).first():
        return jsonify({"message": "User already exists"}), 400

    new_user = User(username=data['username'], email=data['email'])
    new_user.set_password(data['password'])
    db.session.add(new_user)
    db.session.commit()
    return jsonify({"message": "User registered successfully"}), 201

@main.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    user = User.query.filter_by(username=data['username']).first()
    if user is None or not user.check_password(data['password']):
        return jsonify({"message": "Invalid credentials"}), 401

    access_token = create_access_token(identity=user.id)
    return jsonify(access_token=access_token), 200

@main.route('/request_ride', methods=['POST'])
@jwt_required()
def request_ride():
    user_id = get_jwt_identity()
    data = request.get_json()
    new_ride = Ride(user_id=user_id, origin=data['origin'], destination=data['destination'], fare=data['fare'], distance=data['distance'])
    db.session.add(new_ride)
    db.session.commit()
    return jsonify({"message": "Ride requested successfully"}), 201

@main.route('/rides', methods=['GET'])
@jwt_required()
def get_rides():
    user_id = get_jwt_identity()
    rides = Ride.query.filter_by(user_id=user_id).all()
    return jsonify([{"id": ride.id, "origin": ride.origin, "destination": ride.destination, "status": ride.status, "fare": ride.fare, "distance": ride.distance, "start_time": ride.start_time, "end_time": ride.end_time} for ride in rides]), 200

@main.route('/complete_ride/<int:ride_id>', methods=['POST'])
@jwt_required()
def complete_ride(ride_id):
    ride = Ride.query.get_or_404(ride_id)
    ride.status = 'completed'
    ride.end_time = datetime.utcnow()
    db.session.commit()
    return jsonify({"message": "Ride completed successfully"}), 200
[23:09, 9/2/2024] Nacho: from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()
[23:09, 9/2/2024] Nacho: import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import HomeScreen from './screens/HomeScreen';
import LoginScreen from './screens/LoginScreen';
import RegisterScreen from './screens/RegisterScreen';

const Stack = createStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Login">
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Login" component={LoginScreen} />
        <Stack.Screen name="Register" component={RegisterScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
[23:09, 9/2/2024] Nacho: import React, { useState } from 'react';
import { View, Text, TextInput, Button, Alert } from 'react-native';

export default function LoginScreen({ navigation }) {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = async () => {
    try {
      const response = await fetch('http://your-backend-url/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ username, password }),
      });
      const data = await response.json();
      if (response.status === 200) {
        Alert.alert('Login Successful');
        navigation.navigate('Home');
      } else {
        Alert.alert('Login Failed', data.message);
      }
    } catch (error) {
      Alert.alert('Login Error', error.message);
    }
  };

  return (
    <View>
      <Text>Login</Text>
      <TextInput
        placeholder="Username"
        value={username}
        onChangeText={setUsername}
      />
      <TextInput
        placeholder="Password"
        value={password}
        onChangeText={setPassword}
        secureTextEntry
      />
      <Button title="Login" onPress={handleLogin} />
      <Button title="Register" onPress={() => navigation.navigate('Register')} />
    </View>
  );
}
[23:09, 9/2/2024] Nacho: import React, { useState } from 'react';
import { View, Text, TextInput, Button, Alert } from 'react-native';

export default function RegisterScreen({ navigation }) {
  const [username, setUsername] = useState('');
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleRegister = async () => {
    try {
      const response = await fetch('http://your-backend-url/register', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ username, email, password }),
      });
      const data = await response.json();
      if (response.status === 201) {
        Alert.alert('Registration Successful');
        navigation.navigate('Login');
      } else {
        Alert.alert('Registration Failed', data.message);
      }
    } catch (error) {
      Alert.alert('Registration Error', error.message);
    }
  };

  return (
    <View>
      <Text>Register</Text>
      <TextInput
        placeholder="Username"
        value={username}
        onChangeText={setUsername}
