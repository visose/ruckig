<div align="center">
  <h1 align="center">Ruckig</h1>
  <h3 align="center">
    Online Trajectory Generation. Real-time. Time-optimal. Jerk-constrained.
  </h3>
</div>
<p align="center">
  <a href="https://github.com/pantor/ruckig/actions">
    <img src="https://github.com/pantor/ruckig/workflows/CI/badge.svg" alt="CI">
  </a>

  <a href="https://github.com/pantor/ruckig/issues">
    <img src="https://img.shields.io/github/issues/pantor/ruckig.svg" alt="Issues">
  </a>

  <a href="https://github.com/pantor/ruckig/releases">
    <img src="https://img.shields.io/github/v/release/pantor/ruckig.svg?include_prereleases&sort=semver" alt="Releases">
  </a>

  <a href="https://github.com/pantor/ruckig/blob/master/LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-green.svg" alt="LGPL">
  </a>
</p>

Ruckig calculates a time-optimal trajectory given a *target* waypoint with position, velocity, and acceleration, starting from *any* initial state limited by velocity, acceleration, and jerk constraints. Robotics. Machine control. Ruckig is a more powerful and open-source alternative to the [Reflexxes Type IV](http://reflexxes.ws/) library. In fact, Ruckig is a Type V trajectory generator. In general, Ruckig allows for instant reactions to unforeseen events.


## Installation

```bash
mkdir -p build
cd build
cmake -DBUILD_TYPE=Release ..
make
make install
```

A python module can be built using the `BUILD_PYTHON_MODULE` CMake flag.


## Tutorial

Figure. Currently only the (more-complex) *position* interface is implemented.

<div align="center">
  <img width="500" src="https://raw.githubusercontent.com/pantor/ruckig/master/doc/example_profile.png?sanitize=true">
</div>

### Real-time trajectory generation

```c++
Ruckig<6> ruckig {0.001}; // Number DoFs; control cycle in [s]

InputParameter<6> input;
input.current_position = {};
input.current_velocity = {};
input.current_acceleration = {};
input.target_position = {};
input.target_velocity = {};
input.target_acceleration = {};
input.max_velocity = {};
input.max_acceleration = {};
input.max_jerk = {};

OutputParameter<6> output;

while (otg.update(input, output) == Result::Working) {
  // output.new_position

  input.current_position = output.new_position;
  input.current_velocity = output.new_velocity;
  input.current_acceleration = output.new_acceleration;
}

```

### Input Type

```c++
std::array<double, DOFs> current_position;
std::array<double, DOFs> current_velocity {Vector::Zero()};
std::array<double, DOFs> current_acceleration {Vector::Zero()};

std::array<double, DOFs> target_position;
std::array<double, DOFs> target_velocity {Vector::Zero()};
std::array<double, DOFs> target_acceleration {Vector::Zero()};

std::array<double, DOFs> max_velocity;
std::array<double, DOFs> max_acceleration;
std::array<double, DOFs> max_jerk;

std::array<bool, DOFs> enabled;
std::optional<double> minimum_duration;
```


### Result Type

The `update` function of the Ruckig class returns a Result type that indicates the current state of the algorithm. Currently, this can either be **working**, **finished** if the trajectory has finished, or **error** if something went wrong during calculation. In this case, an exception (see below for more details) is thrown.


### Output Type

```c++
std::array<double, DOFs> new_position;
std::array<double, DOFs> new_velocity;
std::array<double, DOFs> new_acceleration;

double duration; // Duration of the trajectory [s]
bool new_calculation; // Whether a new calactuion was performed
double calculation_duration; // Duration of the calculation [µs]
```


### Exceptions


## Tests

The current test suite validates over 190.000 trajectories based on random inputs. Numeric stability is an issue. 
Position, Velocity, and Acceleration target to `1e-8`, Velocity, Acceleration, and Jerk limits to `1e-9`.


## Development

Ruckig is written in C++17. It is currently tested against following versions

- Eigen v3.3.9
- Catch2 v2.13.3 (only for testing)
- Reflexxes v1.2.7 (only for testing)
- Pybind11 v2.6.0 (only for prototyping)
