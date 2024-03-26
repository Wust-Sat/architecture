# WUST Satellite

Satellite project realized at the Wrocław University of Science and Technology
as part of a team project in the summer semester 2023/24.

## Purpouse of the project

At this point the goal is to create an engineering module of a nanosatellite
that will allow testing of available solutions. The engineering module is
intended to allow hardware-in-the-loop tests integrated with CI/CD configured
on Gitlab. EPS should be responsible for both managing the battery charging 
process from solar cells and monitoring the battery status to ensure its 
optimal condition. To save energy, the EPS has the capability to disconnect 
power supply to certain modules. The satellite must have an on-board computer 
able to communicate with the mockup payload and be capable of radio 
communication. The ADCS should incorporate sensors and actuators, and be 
controlled by the OBC. All components should be of a size compatible with the 
CubeSat standard, or it should be possible to miniaturize them in such a way. 
There should be the initiation of simulation enabling the testing of some of 
the satellite's functionalities.

## Milestones

Milestones written based on the need of the hour and formal university
requirements:

 1. Definition of mission objectives.
 2. Completion of the system design phase.
 3. Manufacturing of CubeSat components.
 4. Successful completion of tests for all systems.
 5. Establishing a communication protocol and testing it with the group from
    the ground station.
 6. Completion of the CubeSat construction and integration.

## Budget

Budget will be determined as specific components are selected.

## Contributing

The project takes place on [Gitlab group](). Other people may be invited to
cooperate. Work will be planned during the project development process in
shorter steps. A member's contribution will be reviewed by the others using
Merge Requests. Disputes are resolved by voting by the team and project
manager. Project manager has the deciding vote.

The project is implemented using Gitlab, so stick to the work style suggested
by [Gitlab Flow](https://about.gitlab.com/blog/2023/07/27/gitlab-flow-duo/).

The group will work together, but each member will be responsible for their
own component or subsystem. Each repository must be assigned to a specific
person who is responsible for its consistency.

Merge requests are welcome. For major changes, please open an issue first to
discuss what you would like to change.

## Authors

The project is realized by team from [WUST](https://pwr.edu.pl/) and is carried
out in cooperation with [Tarnowski.io](https://tarnowski.io) company. 

Team Project Members:

1. [**Kacper Drążyk**](https://gitlab.com/kdrazyk):
   - Responsibility: mechanics
   - Competencies: mechanics, 3D CAD and devops.
2. [**Kacper Góralewski**](https://gitlab.com/kgoralewski):
   - Responsibility: team leader, attitude determination and control system
   - Competencies: embedded system development, electronics design, 
      organization of team work, decision-making skills
3. [**Eryk Maśkiewicz**](https://gitlab.com/eryk5683):
   - Responsibility: on-board computer and payload.
   - Competencies: embedded, electronics and computer networks.
4. [**Dominik Pluta**](https://gitlab.com/nmarcin87):
   - Responsibility: electrical power system.
   - Competencies: electronics and PCB design.
5. [**Filip Szkudlarek**](https://gitlab.com/fszkudlarek6):
   - Responsibility: simulation
   - Competencies: embedded system development, programming, CAD,
      interpersonal skills, problem solving skills
6. [**Jakub Sobczyk**](https://gitlab.com/sobczykjakub87):
   - Responsibility: communication system
   - Competencies: embedded system development, electronics design, CAD

Substantive help:

1. [**Juliusz Tarnowski**](https://tarnowski.io):
   - Responsibility:  project manager

## License

Unless otherwise noted, all projects included in this group are under the
[MIT License](https://choosealicense.com/licenses/mit/).