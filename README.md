<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>

<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]

<!-- PROJECT TITLE -->
<br />
<div align="center">
  <a href="https://github.com/Elouahabi-Naoufal/morocco-prayer-times">
    <img src="installer/salah_times.png" alt="Salah Times logo" width="96" height="96">
  </a>

  <h3 align="center">Salah Times — Desktop</h3>

  <p align="center">
    A PyQt5 prayer-times desktop app with a local SQLite cache, a system-tray countdown and
    prayer/iqama notifications.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/morocco-prayer-times"><strong>Explore the repository »</strong></a>
    <br />
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/morocco-prayer-times/issues">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/morocco-prayer-times/issues">Request Feature</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

Salah Times shows the five daily prayers and their iqama times, keeps a live countdown in the
system tray, and posts a desktop notification when a prayer arrives. It started as a Moroccan
prayer-times app: `CITIES` in `app/constants.py` now maps 419 cities across 38 countries to their
yabiladi IDs and localized names, including all 43 Moroccan entries.

Times are **not calculated locally**. The app scrapes the monthly prayer table from each city's page
on yabiladi.com and writes it to `~/.salah_times/salah.db`, one SQLite table per city. After that the
UI is a cache read, so the window paints immediately and continues to work offline. A day counter in
`~/.salah_times/config/day_counter.json` records the last refresh; after 30 days the app offers to
re-scrape.

Implemented behaviour:

* **Main window** — today's times as cards, the current prayer highlighted, a live `HH:MM:SS`
  countdown to the next prayer, and Gregorian plus Hijri dates.
* **Hijri date** — fetched from the aladhan `gToH` endpoint, with an approximation as fallback.
* **Offline mode** — loads today's row from SQLite first and reports remaining cached days; without
  a cache and without a connection it shows an error instead of hanging.
* **Iqama delays** — per-prayer offsets (defaults: Fajr 20, Dhuhr 15, Asr 15, Maghrib 10, Isha 15
  minutes) with a countdown from prayer time to iqama.
* **System tray** — on Linux an `AyatanaAppIndicator3` indicator shows the remaining time and a menu
  with today's schedule, refresh, settings and quit. `app/tray.py` also contains a
  `QSystemTrayIcon` variant that uses `notify-send`/`paplay`.
* **Notifications** — a system sound and notification at prayer time, with per-prayer enable/disable
  and repeat settings.
* **Three languages** — English, Arabic and French, including Arabic month/day and prayer names.
* **City picker** — country drill-down and type-to-filter search.
* **Extra views** — monthly table, 7-day schedule and a multi-city timezone comparison.
* **Single instance** — a PID lock at `~/.salah_times/app.lock` prevents a second copy.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

### Built With

* [![Python][python-shield]][python-url]
* [![PyQt5][pyqt-shield]][pyqt-url]
* [![SQLite][sqlite-shield]][sqlite-url]
* [![Requests][requests-shield]][requests-url]
* [![BeautifulSoup][bs4-shield]][bs4-url]
* [![PyGObject][pygobject-shield]][pygobject-url] — optional, for the Linux AppIndicator tray

Data is read from [yabiladi.com][yabiladi-url]; Hijri dates come from the [aladhan.com][aladhan-url]
API.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* Python 3.6+ (the PyQt5 wheel bundles Qt 5)
* Linux, macOS or Windows
* Internet access on first run, to build the local database
* On Linux, GTK 3 introspection and Ayatana AppIndicator for the tray icon. Without them the main
  window still runs and the tray indicator is skipped.

### Installation

1. Clone the repository
   ```sh
   git clone https://github.com/Elouahabi-Naoufal/morocco-prayer-times.git
   cd morocco-prayer-times
   ```
2. Create a virtual environment and install dependencies
   ```sh
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements_simple.txt
   ```
3. Run the app
   ```sh
   python main.py
   ```
   On first launch, choose a country and city. The scraper builds the SQLite database under
   `~/.salah_times/`.

Optional packaging scripts are included for AppImage, Windows and Android builds
(`build_appimage_updated.sh`, `build_windows.bat`, `build_android_working.sh`,
`installer/installer.nsi`). They are separate from the normal Python run.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE -->
## Usage

* The main window shows today's prayers, a live clock and the next-prayer countdown.
* Use **Refresh** to re-fetch the current city; use **Change Default City** to switch cities.
* **Settings** controls language, iqama offsets, notification behaviour and theme.
* **View** opens the monthly calendar, the weekly schedule and the multi-city timezone view.
* Closing the window keeps the app in the tray. Use the tray menu to show the window, refresh or
  quit.
* After 30 days the app prompts to refresh the cache; declining keeps the existing data.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ROADMAP -->
## Roadmap

- [x] Monthly scraping and per-city SQLite cache
- [x] System-tray countdown and prayer/iqama notifications
- [x] English, Arabic and French UI with RTL support
- [x] Monthly, weekly and multi-city views
- [x] PyInstaller spec and AppImage/Windows/Android build scripts
- [ ] Add an automated test suite for parsing and date normalization
- [ ] Replace the duplicated scraper logic in `app/database.py` and the Android project with a
      shared specification
- [ ] Remove committed `__pycache__` files from the repository
- [ ] Add a CI job that lints and smoke-tests the app on Linux

See the [open issues](https://github.com/Elouahabi-Naoufal/morocco-prayer-times/issues) for a full list of proposed features and known issues.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/morocco-prayer-times/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/morocco-prayer-times" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->
## License

No `LICENSE` file is bundled with this repository. This project is not licensed — all rights reserved.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTACT -->
## Contact

Naoufal Elouahabi
* LinkedIn: [https://www.linkedin.com/in/elouahabi-naoufal/](https://www.linkedin.com/in/elouahabi-naoufal/)
* Email: [elouahabi-naoufal1@gmail.com](mailto:elouahabi-naoufal1@gmail.com)

Project Link: [https://github.com/Elouahabi-Naoufal/morocco-prayer-times](https://github.com/Elouahabi-Naoufal/morocco-prayer-times)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [yabiladi.com](https://www.yabiladi.com/prieres/) for the published prayer-time tables
* [aladhan.com](https://aladhan.com/) for the Hijri-date API
* [PyQt5](https://riverbankcomputing.com/software/pyqt/)
* [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)
* [Img Shields](https://shields.io)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/morocco-prayer-times.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/morocco-prayer-times/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/morocco-prayer-times.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/morocco-prayer-times/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/morocco-prayer-times.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/morocco-prayer-times/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/morocco-prayer-times.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/morocco-prayer-times/issues
[license-shield]: https://img.shields.io/badge/License-All%20rights%20reserved-red.svg?style=for-the-badge
[license-url]: https://github.com/Elouahabi-Naoufal/morocco-prayer-times
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[python-shield]: https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white
[python-url]: https://www.python.org/
[pyqt-shield]: https://img.shields.io/badge/PyQt5-41CD52?style=for-the-badge&logo=qt&logoColor=white
[pyqt-url]: https://riverbankcomputing.com/software/pyqt/
[sqlite-shield]: https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white
[sqlite-url]: https://www.sqlite.org/
[requests-shield]: https://img.shields.io/badge/Requests-2CA5E0?style=for-the-badge&logo=python&logoColor=white
[requests-url]: https://requests.readthedocs.io/
[bs4-shield]: https://img.shields.io/badge/BeautifulSoup4-43B02A?style=for-the-badge&logo=python&logoColor=white
[bs4-url]: https://www.crummy.com/software/BeautifulSoup/
[pygobject-shield]: https://img.shields.io/badge/PyGObject-4A86CF?style=for-the-badge&logo=gnome&logoColor=white
[pygobject-url]: https://pygobject.readthedocs.io/
[yabiladi-url]: https://www.yabiladi.com/prieres/
[aladhan-url]: https://aladhan.com/
