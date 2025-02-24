# EMNLP 2023 Virtual Conference

This virtual conference page is based on [MiniConf](http://www.mini-conf.org/) by Alexander Rush and Hendrik Strobelt.
It was extended by the [amazing team of ACL 2020](https://github.com/acl-org/acl-2020-virtual-conference) and [amazing team of ACL 2023](https://github.com/acl-org/acl-2023-miniconf/).
It was adapted for EMNLP 2023 by the virtual infrastructure committee.

The website is based on [Flask](https://flask.palletsprojects.com/) and [Frozen-Flask](https://pythonhosted.org/Frozen-Flask/). It:

1. Parses conference-specific input data (e.g., TSV files downloaded from Google Sheets) into a common format.
2. From common format data, generate a full static page site, which can be deployed easily.

## Quick Start (No Program Updates)

1. Install [python poetry](https://python-poetry.org/) 

Tips: The official installation process may not be suitable for all network situations. 
It is recommended to proceed directly in a Python=3.10 environment by: `pip install poetry`

2. Run: `poetry install`

Tips: During this step, it cannot be installed directly, and this variable needs to be introduced first:
`export PYTHON_KEYRING_BACKEND=keyring.backends.null.Keyring`

3. Run: `make run`
4. Visit `localhost:7777`

When you are ready to deploy run `make freeze` to get a static version of the site in the `build` folder.

This will not pickup any program changes, but it will pickup source code changes on the next GH Pages deploy.

## Contributing

We are very welcoming of community contributions!

- Bug Reports & Feature Requests: Open an issue giving reproducible steps
- Contributions:
    - Clone the repo
    - Run "Quick Start" steps above and check site works
    - Make changes
    - Ensure `make freeze` runs without errors (warnings are fine)
    - Make a pull request.
    - If the changes look good to us and the site builds on GH Actions, we will merge and your changes will be live for everyone to see!

## Update EMNLP 2023 [Website]

We did the following steps to adopt the website for EMNLP 2023 Miniconf:
1. Change the global configs (configs/site.yaml)
2. Add the picture the website need (static/images/emnlp2023/)
3. Modify the front-pages (templates/)
4. Create the EMNLP 2023 website data dir (data/emnlp_2023/)

## Update EMNLP 2023 from Source  

EMNLP 2023 conference data is generated by one step if you have prepared data:

1. Data is located in `data/emnlp_2023/data` and should be downloaded from the (private) conference google spreadsheets into the file: `input.xlsx`,`oral-papers.tsv`and`poster-demo-papers.tsv`. 
2. Run `sh pipeline.sh` to generate data to `conference.json`. (all same content)

## Update ACL 2023 from Source

ACL 2023 Miniconf is generated in three steps:

1. Data is located in `data/acl_2023/data` and should be downloaded from the (private) conference google spreadsheets into these files: `oral-papers.tsv`, `poster-demo-papers.tsv`, `spotlight-papers.tsv`, and `virtual-papers.tsv`. There are additional files in the same google drive, make sure to download all of them to that folder.
2. Run `python acl_miniconf/import_acl2023.py` to generate data to `auto_data/acl_2023/`, in `conference.json`, `conference.pkl`, and `conference.yaml` (all same content).
3. Run `make run`
4. Before pushing, run `make freeze` and check it works

### Update ACL 2023 Workshops from Source

Aggregated workshop paper proceedings are stored as public source data in `data/acl_2023/data` which are subsequently parsed by the `import_acl2023.py` script. The file `data/acl_2023/data/workshop_papers.yml` can be generated by running:
1. Running `scripts/clone_workshops.sh`
2. Running `python acl_miniconf/import_acl2023_workshop_papers.py`

Following this, be sure to run the script to remake `conference.json`, `python acl_miniconf/import_acl2023.py`

## Todos

- Obtain and incorporate info beyond paper program
- Obtain/incorporate underline links for papers/events

## Project Structure

The repository consists of the following main components:

1) *Datastore* [sitedata](https://github.com/acl-org/emnlp-2020-virtual-conference/tree/master/sitedata)

Collection of data files representing the papers, speakers, workshops, and other important information for the conference.

2) *Routing* [main.py](https://github.com/acl-org/emnlp-2020-virtual-conference/tree/master/main.py)

This file contains defines the Flask app and the routes

3) *Templates* [templates](https://github.com/acl-org/emnlp-2020-virtual-conference/tree/master/templates)

Contains all the pages for the site. See `base.html` for the master page and `components.html` for core components.

4) *Frontend* [static](https://github.com/acl-org/emnlp-2020-virtual-conference/tree/master/static)

Contains frontend components like the default css, images, and JavaCcript libs.

5) *Scripts* [scripts](https://github.com/acl-org/emnlp-2020-virtual-conference/tree/master/scripts)

Contains additional preprocessing to add visualizations, recommendations, schedules to the conference. 

## Data processing Architecture

1. `acl_miniconf` uses a shared data format that current and future conferences should use defined in `acl_miniconf.data.Conference`. 
2. `acl_miniconf/import_acl2023.py`: Parses program information from spreadsheets/underline to generate a single `acl_miniconf.data.Conference` object, which is converted to JSON for the frontend and YAML for human inspection.
3. The static components of the website consume the JSON output to generate the website.

The main reasons for this architecture are:

1. To abstract the data parsing from the data format.
2. Keep a static format for the frontend.
3. Move conference specific processing out from shared library code.


## RocketChat Documentation

Once you have a RocketChat instance, be sure to:

1. How to get API key: https://docs.rocket.chat/use-rocket.chat/user-guides/user-panel/my-account
2. Copy the template in `configs/rocketchat/template.yaml` to another file in the same directory. Add your user/api keys here, but do not commit it!
3. Follow these instructions and ensure that getting all paginated results is allowed: https://developer.rocket.chat/reference/api/rest-api#pagination

Running the CLI, from root directory: `python -m acl_miniconf.rocketchat.cli --config-name rc-pedro command=add_emojis`

## Pages

This section describes all pages that are in this version of MiniConf and how to customize them.

### Index

This page is mainly configured via `sitedata/config.yml`. One mainly needs to change the conference name, date,
number of workshops/tutorials and help documents here. Before the conference starts, also update the
acknowledgements. In `static/js/time-extend.js`, you also need to change start and end times of the conference to 
make sure that daylight saving is handled correctly.

### Schedule

The schedule is based on [FullCalendar](https://fullcalendar.io/). The entries are automatically generated from
the data of the paper sessions/workshops/plenary. For the weekly view, we compute blocks of events and show only a
generic name, on the day view, they are shown as is. Additional events can be added via `sitedata/overall_calendar.yml`.
If you add new event types, make sure to assign them a color in `load_site_data::build_schedule`.

### Plenary

Plenary make up the official program of the conference. For EMNLP, we had live events and prerecorded keynote talks. Keynotes
were prerecorded but livestreamed via SlidesLive. Panels were fully live and used CART real-time captioning. We did not 
add the prerecorded talks to the plenary page before the talk was done. Each plenary details page can either have one 
video or a list of videos. It can also have a RocketChat channel. The default is to just show one SlidesLive video. Refer to 
[the ACL2020 repo](https://raw.githubusercontent.com/acl-org/acl-2020-virtual-conference/master/sitedata_acl2020/business_meeting.csv)
for how multiple videos can be shown on a plenary details page. 

### Livestream

Plenary events were livestreamed via SlidesLive. Make sure that your contract with them covers this.
Panels were fully live and used CART real-time captioning.

For EMNLP, we had prerecorded presentations and keynote speakers and live panels. Recordings were streamed live. For keynotes, after the 
prerecorded talk was shown, the keynote speaker, a volunteer and a SlidesLive person were in a Zoom call, this Zoom call was 
streamed after seamlessly after the keynote recording. Then the volunteer took questions from the `#live` RocketChat channel 
and asked the keynote speaker.

Before the livestream starts, change the livestream ID and CART URL on `livestream.html`. The Livestream SlidesLive IDs 
are different from the actual ID for the recording, e.g. if a keynote is livestreamed,
then the livestream ID is different from the prerecorded ID. Make sure to update the ID before the stream start
and ask SlidesLive to show a `Livestream will start soon` message. After the livestream is done, add the ID
to the plenary event itself. You can use the livestream ID as a slideslive ID for the player, it will then show
a recording of the livestream.

#### Visualization

We use [SPECTER](https://github.com/allenai/specter) to generate document embeddings from abstracts and
[DyGIE++](https://github.com/dwadden/dygiepp) to generate keywords. For the embeddings, we then use `umap`
to project them to 2D. Recommentations are generated by using n-nearest neighbours.

We provide `scripts/dataentry/projections.py` to generate the projections, please
refer to the respective repositories to find out how to install them. Install them in their seperate `virtualenv`,
they all have conflicting dependencies.

#### Images

We extract images from the PDFs and upload them to Amazon S3. You can use our bad script under
`scripts/dataentry/extract_images.py` to extract them. In order to allow authors to change their images,
we set up [an additional Github repository](https://github.com/acl-org/emnlp-2020-virtual-conference-images) that 
contains the images. Pull requests there automatically deploy the update images to S3. Look at the Github workflow
there and set the respective secrets for the autodeploy.

#### ConnectedPapers

[Connected Papers](https://www.connectedpapers.com/) is a visual tool to help researchers and applied scientists find 
academic papers relevant to their field of work. In addition to linking to the connected papers for each main paper, 
they built a custom page for EMNLP which is shown below every main paper presentation. If you want that also, then 
conference papers need to be indexed by SemanticScholar before the conference. Contact them via their website and ask 
nicely, then they will also help you.

### Tutorials

Tutorials simply contain a schedule of the events, website, Zoom links, RocketChat and optional a prerecorded SlivesLive
video. We asked tutorial organizers to fill in the information into Google Sheets and then use a script to download that
sheet and parse it. You can refer to `scripts/dataentry/tutorials.py` and see how we loaded tutorials. We used 
[this template](https://docs.google.com/spreadsheets/d/1jvAU8yNLFqQj8-iehjYwxdRsopQq78NUk0cwl2zW6ak/edit?usp=sharing) for 
tutorial organizers to fill in. Tutorial blocks on the tutorial overview page and in the main schedule are computed 
automatically.

### Workshops

Workshops contain a schedule of the events (can use markdown in there, website, Zoom links, RocketChat, a list of prerecorded
SlivesLive videos for invited talks and a link to workshop papers. We asked workshop organizers to fill in the information 
into Google Sheets and then use a script to download that sheet and parse it. You can refer to `scripts/dataentry/workshop.py`
and see how we loaded workshops. We used 
[this template](https://docs.google.com/spreadsheets/d/1LePFp66Q5v9LLNgkyQmHlzEvwFo21YJrvpvydf28yBs/edit?usp=sharing) for 
workshop organizers to fill in. Workshop blocks on the workshop overview page and in the main schedule are computed automatically.
We gave each workshop up to 5 Zoom links, but did not schedule events for them. Instead, we linked the personal meeting ID
on our website.

### Socials

We asked social organizers to fill in the information into Google Sheets and then use a script to download that sheet and parse 
it. You can refer to `scripts/dataentry/socials.py` and see how we loaded socials. We used 
[this template](https://docs.google.com/spreadsheets/d/1IDk3K1JD1hvH_hvyMy6TeRuE2F6DQDfpgwNpTIP9KgI/edit?usp=sharing) for 
social organizers to fill in. Social blocks in the main schedule are computed automatically.
We gave each social event one Zoom link or they could get a Gather room, but did not schedule events for them. Instead, we 
linked the personal meeting ID on our website.

#### Relevant documents

- [Invitation to Dry Run](https://docs.google.com/document/d/1f0ScAG_tUNZry4F5cVBkfwtKaftnKJfZEn7nJZvkxd0/edit?usp=sharing)
- [EMNLP Instructions to sponsors](https://docs.google.com/document/d/1p6ZsN8WmtZiHG0Oyh2zY8eQmSNxH8lzMDHGIWr36LZI/edit?usp=sharing)

### Chat

We use RocketChat throughout this page to have channels for keynotes, plenaries, live, papers, workshops, tutorials
and many more. You can host RocketChat yourself, e.g. on AWS (refer to [their guide](https://docs.rocket.chat/installation))
or pay them to host it. We normally first create a demo workspace and then upgrade it to full for one month with the 
number of anticipated users during the conference.

We integrate RocketChat via SSO into our Amazon Cognito user repository so that only one set of username and password 
is needed. For that, you can refer to [this guide](https://github.com/acl-org/acl-2020-virtual-conference/issues/53).
You do not need to change the Lambda functions if you set up the project correctly when creating the AWS app. 

You can refer to our [checklist for RocketChat](https://github.com/acl-org/emnlp-2020-virtual-conference/issues/49) to
have an idea what needs to be done. 

We have scripts for RocketChat setup in `scripts\rocketchat` and [here](https://github.com/acl-org/acl-2020-virtual-conference-tools).

In order to use the `Active Chat` feature, you need to use RocketChat and host the statistics server. Please refer to
`scripts/channels-stats` to see how. If you do not want it, remove it from `base.html`.


## Misc

### Gather.Town

We use [VirtualChair](https://www.virtualchair.net/) to manage Gather.Town for us. We strongly recommend to also
book them. Ask them early enough. We use SSO with Gather.Town, you need to specifically ask for it and make 
sure that it lands in the contract.

### Zoom

We buy one Master account and then create accounts for the specific events. For things like tutorials, socials and workshops
we do not schedule events but use the personal meeting link. You can use
[these scripts](https://github.com/acl-org/acl-2020-virtual-conference-tools) to help you creating Zoom things.

#### Relevant documents

- [Volunteers’ Documentation on Zoom](https://docs.google.com/document/d/1MFg1CnSd2Vu4oS30MXqJt4v3c1XozHX_1DPGxkXqXeA/edit?usp=sharing)
- [Sponsors’ Documentation on Zoom](https://docs.google.com/document/d/1CPekSelpKNjGMTH7okFmCzEGaCqFmqSu2HVfYRRSWiU/edit?usp=sharing)
- [Mentors’ Documentation on Zoom](https://docs.google.com/document/d/10aVXLFKQH95cCJ-JoauhzNztBjcu_2iYw-Pz9ETFlS8/edit?usp=sharing)


### Favicons

We use [this website](https://realfavicongenerator.net/) to generate favicons from an image.

## Acknowledgements

MiniConf was built by [Hendrik Strobelt](http://twitter.com/hen_str) and [Sasha Rush](http://twitter.com/srush_nlp).

Thanks to Darren Nelson for the original design sketches. Shakir Mohamed, Martha White, Kyunghyun Cho, Lee Campbell, 
and Adam White for planning and feedback. Hao Fang, Junaid Rahim, Jake Tae, Yasser Souri, Soumya Chatterjee, and Ankshita 
Gupta for contributions. 

It was extended by the [ACL 2020 virtual infrastructure team](https://acl2020.org/committees/organization), especially
by Hao Fang and Sudha Rao [with the help of many volunteers](https://virtual.acl2020.org/static/pdf/virtual_infrastructure_volunteers.pdf).

It was extended for EMNLP 2020 by [Jan-Christoph Klie](https://github.com/jcklie) with the help of the 
[EMNLP 2020 virtual infrastructure team](https://2020.emnlp.org/organizers) and 
[with the help of many volunteers](static/pdf/volunteers.pdf).




