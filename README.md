# SLO Calculator

This project is a simple web application that calculates the Service Level Objective (SLO) burn rate, error budgets, and downtime allocation based on user inputs. It's built using Svelte and styled with Tailwind CSS.

## Features

- Calculate SLO burn rate based on input parameters
- Real-time updates as user changes inputs
- Validation for SLO target percentage
- Responsive design

## Prerequisites

- Node.js (v14 or later recommended)
- npm (comes with Node.js)

## Installation

1. Clone the repository:
   ```
   git clone https://github.com/yourusername/slo-burn-rate-calculator.git
   cd slo-burn-rate-calculator
   ```

2. Install dependencies:
   ```
   npm install
   ```

3. Start the development server:
   ```
   npm run dev
   ```

4. Open your browser and navigate to `http://localhost:5173/` (or the port specified in your console output).

## Usage

1. Enter the length of SLO target in days.
2. Input the percentage of error budget consumed.
3. Specify the long window in hours.
4. Set the SLO target percentage.
5. The application will automatically calculate and display the burn rate.
6. The short window (in minutes) is calculated based on the long window.
7. The app will validate if the burn rate threshold is appropriate for your SLO target percentage.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Open Source Love

This project is developed with love by open source enthusiasts. We believe in the power of community-driven development and welcome contributions from developers around the world. Your ideas, bug reports, and pull requests are greatly appreciated!

## License

This project is open source and available under the [GPL v3 License](LICENSE).

## More Information

For more information about this project and other related work, please visit [https://hec.works](https://hec.works).

## Contact

If you have any questions or feedback, please open an issue in the GitHub repository.