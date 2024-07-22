---
layout: nba
title: Classifying FGA in Tracking Data
---

## Data Preparation

```python
def unzip_7z(source_path, destination_path):

	with py7zr.SevenZipFile(source_path, mode = "r") as zipped_file:
	    zipped_file.extractall(destination_path)

unzip_7z(
	source_path = "./data/2016.NBA.Raw.SportVU.Game.Logs/10.28.2015.SAS.at.OKC.7z",
	destination_path = "./data/games_json/"
	)
```