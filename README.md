# Weather app_Project
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() {
  runApp(const WeatherApp());
}

class WeatherApp extends StatelessWidget {
  const WeatherApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Weather App',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: const WeatherHomePage(),
    );
  }
}

class WeatherHomePage extends StatefulWidget {
  const WeatherHomePage({super.key});

  @override
  State<WeatherHomePage> createState() => _WeatherHomePageState();
}

class _WeatherHomePageState extends State<WeatherHomePage> {
  String city = 'London';
  String apiKey = 'YOUR_API_KEY'; // Replace with your OpenWeatherMap API key
  Map<String, dynamic>? weatherData;

  Future<void> fetchWeather(String cityName) async {
    final url = Uri.parse(
        'https://api.openweathermap.org/data/2.5/weather?q=$cityName&appid=$apiKey&units=metric');
    final response = await http.get(url);

    if (response.statusCode == 200) {
      setState(() {
        weatherData = json.decode(response.body);
      });
    } else {
      setState(() {
        weatherData = null;
      });
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('City not found. Please try again.')),
      );
    }
  }

  @override
  void initState() {
    super.initState();
    fetchWeather(city);
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Weather App'),
        centerTitle: true,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            TextField(
              decoration: const InputDecoration(
                labelText: 'Enter city name',
                border: OutlineInputBorder(),
              ),
              onSubmitted: (value) {
                setState(() {
                  city = value;
                });
                fetchWeather(city);
              },
            ),
            const SizedBox(height: 20),
            weatherData != null
                ? Column(
                    children: [
                      Text(
                        '${weatherData!['name']}, ${weatherData!['sys']['country']}',
                        style: const TextStyle(
                          fontSize: 24,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                      const SizedBox(height: 10),
                      Text(
                        '${weatherData!['main']['temp']}°C',
                        style: const TextStyle(
                          fontSize: 40,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                      const SizedBox(height: 10),
                      Text(
                        '${weatherData!['weather'][0]['description']}',
                        style: const TextStyle(
                          fontSize: 20,
                        ),
                      ),
                    ],
                  )
                : const Center(
                    child: Text(
                      'No weather data available.',
                      style: TextStyle(fontSize: 18),
                    ),
                  ),
          ],
        ),
      ),
    );
  }
}
