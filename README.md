<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>




<!-- PROJECT SHIELDS -->
<!--
*** I'm using markdown "reference style" links for readability.
*** Reference links are enclosed in brackets [ ] instead of parentheses ( ).
*** See the bottom of this document for the declaration of the reference variables
*** for contributors-url, forks-url, etc. This is an optional, concise syntax you may use.
*** https://www.markdownguide.org/basic-syntax/#reference-style-links
-->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]
[![LinkedIn][linkedin-shield]][linkedin-url]




<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/Elouahabi-Naoufal/Salah-Morocco">
    <img src="installer/salah_times.png" alt="Salah Times logo" width="80" height="80">
  </a>

  <h3 align="center">Salah Times</h3>

  <p align="center">
    A PyQt5 prayer-times desktop app with an offline SQLite cache, a system-tray countdown
    and desktop notifications for prayer and iqama.
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/Salah-Morocco"><strong>Read the source »</strong></a>
    <br />
    <br />
    <a href="https://github.com/Elouahabi-Naoufal/Salah-Morocco/issues">Report Bug</a>
    &middot;
    <a href="https://github.com/Elouahabi-Naoufal/Salah-Morocco/issues">Request Feature</a>
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

Salah Times shows the five daily prayers plus their iqama times, keeps running in the system tray with a
live countdown, and fires a desktop notification when a prayer time arrives. It started as a Moroccan
prayer-times app and now covers 43 Moroccan cities plus a much longer international list — `CITIES` in
`app/constants.py` maps 38 countries to their yabiladi city IDs and localised names.

The interesting part is the data path. Times are **not** computed locally: the app scrapes the monthly
prayer table from each city's page on yabiladi.com, then writes the result into a local SQLite file at
`~/.salah_times/salah.db`, one table per city. From then on the UI is a pure cache read, so the window
paints instantly and keeps working with no network at all. A day counter in
`~/.salah_times/config/day_counter.json` records the last refresh; after 30 days the app offers to
re-scrape.

Feature by feature, as implemented:

* **Main window** — today's times as cards, the current prayer highlighted, a live `HH:MM:SS` countdown to
  the next prayer, Gregorian and Hijri dates.
* **Hijri date** — fetched from the aladhan `gToH` endpoint, with a computed approximation as the
  fallback when the call fails.
* **Offline mode** — if the SQLite cache already covers today, the app loads from it immediately and shows
  how many days of data remain. Without a cache and without a connection it reports the error instead of
  hanging.
* **Iqama delays** — per-prayer offsets (defaults: Fajr 20, Dhuhr 15, Asr 15, Maghrib 10, Isha 15 minutes)
  with a countdown from prayer time to iqama.
* **System tray** — on Linux an `AyatanaAppIndicator3` indicator shows the remaining time in its label and
  carries a menu with today's schedule, refresh, settings and quit. `app/tray.py` also holds a standalone
  `QSystemTrayIcon` variant that uses `notify-send` and `paplay` for notifications and sound.
* **Notifications** — at prayer time the app plays a system sound and posts a notification; each prayer can
  be disabled individually or repeated up to a configured count at a configurable interval.
* **Three languages** — English, Arabic and French, including Arabic month/day names and prayer names. The
  tray re-reads the config every two seconds, so switching language in the main window updates the tray
  without a restart.
* **City picker** — drill down by country, filter as you type, with city names localised in the active
  language.
* **Extra views** — a monthly table with month navigation, a 7-day schedule with week navigation, and a
  multi-city comparison table for checking times across timezones.
* **Single instance** — a PID lock file at `~/.salah_times/app.lock` prevents a second copy from starting.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



### Built With

* [![Python][Python.com]][Python-url]
* [![PyQt5][PyQt5.com]][PyQt5-url]
* [![SQLite][SQLite.com]][SQLite-url]
* [![Requests][Requests.com]][Requests-url]
* [![BeautifulSoup4][BeautifulSoup4.com]][BeautifulSoup4-url]
* [![PyGObject][PyGObject.com]][PyGObject-url]
* [![PyInstaller][PyInstaller.com]][PyInstaller-url]
* [![AppImageKit][AppImageKit.com]][AppImageKit-url]

Data is read from [yabiladi.com][yabiladi-url]; Hijri dates come from the [aladhan.com][aladhan-url] API.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Getting Started

### Prerequisites

* Python 3.6+ and a working Qt 5 install (the `PyQt5` wheel ships its own).
* Linux, macOS or Windows.
* Internet access on first run, to build the local database.
* On Linux, the tray indicator needs GTK 3 introspection plus Ayatana AppIndicator. Without it the main
  window still runs, but the tray icon is skipped:

  ```sh
  sudo apt install python3-gi gir1.2-gtk-3.0 gir1.2-ayatanaappindicator3-0.1
  ```

  Notifications use `notify-send` and `paplay` (PulseAudio) and fall back to a Qt tray balloon or a
  terminal bell if either binary is missing.

### Installation

1. Clone the repository

   ```sh
   git clone https://github.com/Elouahabi-Naoufal/Salah-Morocco.git
   cd Salah-Morocco
   ```

2. Create a virtual environment and install the dependencies

   ```sh
   python3 -m venv venv
   source venv/bin/activate          # Windows: venv\Scripts\activate
   pip install -r requirements_simple.txt
   ```

3. Run the app

   ```sh
   python3 main.py
   ```

   On the first launch a city-selection dialog appears. Pick your city, and the app scrapes and caches
   every city in the table in the background — that first run takes a while, so leave it running until the
   database fills in.

4. Change the git remote to your fork before committing

   ```sh
   git remote set-url origin https://github.com/Elouahabi-Naoufal/Salah-Morocco.git
   git remote -v # confirm the changes
   ```

<details>
<summary>Build distributables</summary>

**Linux AppImage** — `build_appimage_updated.sh` creates a virtualenv, freezes `main.py` with the
`SalahTimes` name, bundles the Ayatana/Gtk typelibs and `libayatana-appindicator3`, and packs the AppDir
with `appimagetool`:

```sh
chmod +x build_appimage_updated.sh
./build_appimage_updated.sh       # -> SalahTimes-x86_64.AppImage
```

**Windows executable** — from a Windows shell, `build_windows.bat` produces `dist\SalahTimes.exe` as a
one-file, windowed PyInstaller binary.

**Windows installer** — `build_installer.sh` runs PyInstaller and NSIS under Wine and emits
`SalahTimes-Setup.exe`, which installs to `C:\Program Files\Salah Times` with Start Menu and desktop
shortcuts and registers an uninstaller:

```sh
./build_installer.sh              # needs Wine + NSIS makensis
```

**Android** — `buildozer.spec` and `build_android_working.sh` are left in the tree from the earlier
Kivy-based version. They do not currently build: the spec expects a Kivy entry point, the current
`main.py` is PyQt5, and the script installs an `android_requirements.txt` that is not in the repository.
Treat the Android target as unfinished rather than as a working path.

</details>

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- USAGE EXAMPLES -->
## Usage

### First run

Choose a language and a city in the welcome dialog. The city is stored in
`~/.salah_times/config/app_config.json`; from then on the app reconnects to the last city and restores
the window geometry on startup.

### Reading today's times

The main window shows Fajr, Dhuhr, Asr, Maghrib and Isha as cards. The prayer whose window you are in is
highlighted, and the countdown below tracks the next prayer to the second. Once a prayer time is reached,
the countdown switches to the time remaining until iqama.

Click the refresh button to re-read the cache, or use **Settings → Refresh DB** to re-scrape every city
from the network. When the 30-day counter expires the app prompts for a refresh at launch.

### Views

The **View** menu opens three dialogs:

| View | What it shows |
|------|---------------|
| Monthly calendar | One row per day for the selected month, with all five prayer times; navigate months with the arrows |
| Weekly schedule | A 7-day table, shiftable a week at a time |
| Multiple timezones | Today's times for the selected cities side by side; **+ Add City** appends any city from `CITIES` |

All three read from the local SQLite cache, so they are as fast as the main window and work offline.

### Settings

* **General** — language, default city, window geometry.
* **Iqama** — per-prayer delay in minutes, with a reset-to-defaults button.
* **Notifications** — global sound toggle, repeat interval, and per-prayer enable/repeat-count.

Changes are written to `~/.salah_times/config/` as JSON. The tray watches `app_config.json` for language and
city changes and reloads within about two seconds.

### Configuration files

| Path | Contents |
|------|----------|
| `~/.salah_times/salah.db` | SQLite cache, one table per city (`date`, `fajr`, `dohr`, `asr`, `maghreb`, `isha`) |
| `~/.salah_times/app.lock` | PID of the running instance |
| `~/.salah_times/config/app_config.json` | Selected city and language |
| `~/.salah_times/config/iqama_times.json` | Per-prayer iqama offsets |
| `~/.salah_times/config/notifications.json` | Notification and sound preferences |
| `~/.salah_times/config/day_counter.json` | Timestamp of the last full refresh |
| `~/.salah_times/tray/tray_config.json` | Tray-specific state |

Delete `salah.db` to force a clean re-scrape; delete `app.lock` if a crash leaves it behind and blocks
startup.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ROADMAP -->
## Roadmap

Working in the current tree:

- [x] Local SQLite cache for every city, refreshed in a background thread
- [x] Offline operation with a days-remaining indicator
- [x] Main window with live countdown, current-prayer highlight and Hijri date
- [x] Ayatana AppIndicator tray icon on Linux, plus a standalone Qt tray in `app/tray.py`
- [x] Prayer-time notifications with per-prayer repeats and iqama delays
- [x] English / Arabic / French UI, with the tray picking up language changes live
- [x] Monthly, weekly and multi-city views
- [x] AppImage, Windows EXE and NSIS installer builds

Known gaps, taken from the code as it stands:

- [ ] No export of the monthly view. Earlier docs mentioned PDF export; there is no printing or PDF code
      in the tree, even though `PyQt5.QtPrintSupport` is still listed as a hidden import in the spec files.
- [ ] The Android path is stale — `buildozer.spec` targets a Kivy app that no longer exists in this tree.
- [ ] The tray indicator is Linux/GNOME-specific. `salah-tray.desktop` still points at an absolute path on
      the author's machine rather than a script in the repository.
- [ ] Scraper maintenance: `app/database.py` picks the largest 6-column `table.prayer-table` on the page,
      so a yabiladi layout change breaks the refresh without necessarily breaking the app — cached data
      keeps working until it runs out.
- [ ] Coordinates are only filled in for the Moroccan cities; the international entries have `lat`/`lon`
      set to `None` and are not used for anything yet.
- [ ] No macOS build script.

See the [open issues](https://github.com/Elouahabi-Naoufal/Salah-Morocco/issues) for a full list of proposed features (and known issues).

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

If you touch `app/constants.py`, please add the new city to **both** `CITIES` and `CITY_SLUGS` — the
lookup requires an entry in each, and a missing slug silently drops the city from the refresh.

### Top contributors:

<a href="https://github.com/Elouahabi-Naoufal/Salah-Morocco/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=Elouahabi-Naoufal/Salah-Morocco" alt="contrib.rocks image" />
</a>

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- LICENSE -->
## License

No license file is currently bundled in this repository, so the terms are still to be settled. Earlier
versions of this README described the project as MIT, but no `LICENSE` file was ever committed — treat
that as an oversight to be fixed rather than a grant. Open an issue if you need a clear answer before
using the code.

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- CONTACT -->
## Contact

Naoufal Elouahabi - [LinkedIn](https://www.linkedin.com/in/elouahabi-naoufal/) - elouahabi-naoufal1@gmail.com

Project Link: [https://github.com/Elouahabi-Naoufal/Salah-Morocco](https://github.com/Elouahabi-Naoufal/Salah-Morocco)

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

* [yabiladi.com](https://www.yabiladi.com/) — source of the prayer-time tables this app caches
* [Al Adhan API](https://aladhan.com/) — Gregorian to Hijri date conversion
* [Ayatana AppIndicator](https://github.com/ayatana-appindicator/ayatana-appindicator) — GNOME tray icon
* [Riverbank Computing — PyQt5](https://www.riverbankcomputing.com/software/pyqt/)
* [AppImageKit](https://github.com/AppImage/AppImageKit)
* [Img Shields](https://shields.io)
* [Choose an Open Source License](https://choosealicense.com)

<p align="right">(<a href="#readme-top">back to top</a>)</p>




<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/Elouahabi-Naoufal/Salah-Morocco.svg?style=for-the-badge
[contributors-url]: https://github.com/Elouahabi-Naoufal/Salah-Morocco/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/Elouahabi-Naoufal/Salah-Morocco.svg?style=for-the-badge
[forks-url]: https://github.com/Elouahabi-Naoufal/Salah-Morocco/network/members
[stars-shield]: https://img.shields.io/github/stars/Elouahabi-Naoufal/Salah-Morocco.svg?style=for-the-badge
[stars-url]: https://github.com/Elouahabi-Naoufal/Salah-Morocco/stargazers
[issues-shield]: https://img.shields.io/github/issues/Elouahabi-Naoufal/Salah-Morocco.svg?style=for-the-badge
[issues-url]: https://github.com/Elouahabi-Naoufal/Salah-Morocco/issues
[license-shield]: https://img.shields.io/badge/license-see%20repository-informational?style=for-the-badge
[license-url]: https://github.com/Elouahabi-Naoufal/Salah-Morocco
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://www.linkedin.com/in/elouahabi-naoufal/
[Python.com]: https://img.shields.io/badge/python-3776AB?style=for-the-badge&logo=python&logoColor=white
[Python-url]: https://www.python.org/
[PyQt5.com]: https://img.shields.io/badge/PyQt5-2C7BB4?style=for-the-badge&logo=qt&logoColor=white
[PyQt5-url]: https://www.riverbankcomputing.com/software/pyqt/
[SQLite.com]: https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white
[SQLite-url]: https://www.sqlite.org/
[Requests.com]: https://img.shields.io/badge/requests-2C5D8A?style=for-the-badge&logo=python&logoColor=white
[Requests-url]: https://requests.readthedocs.io/
[BeautifulSoup4.com]: https://img.shields.io/badge/BeautifulSoup-43A047?style=for-the-badge&logo=python&logoColor=white
[BeautifulSoup4-url]: https://www.crummy.com/software/BeautifulSoup/
[PyGObject.com]: https://img.shields.io/badge/PyGObject-4A8B2C?style=for-the-badge&logo=gnome&logoColor=white
[PyGObject-url]: https://pygobject.gnome.org/
[PyInstaller.com]: https://img.shields.io/badge/PyInstaller-3C1387?style=for-the-badge
[PyInstaller-url]: https://pyinstaller.org/
[AppImageKit.com]: https://img.shields.io/badge/AppImage-3373AB?style=for-the-badge
[AppImageKit-url]: https://github.com/AppImage/AppImageKit
[yabiladi-url]: https://www.yabiladi.com/
[aladhan-url]: https://aladhan.com/