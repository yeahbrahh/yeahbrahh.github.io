# My CS Projects

## Project: Refactoring and Extending a Travel Optimization System

### Overview

In this course, my team worked on improving a large, existing Java-based system that generated optimized travel routes between destinations. The goal was to refactor legacy code, improve structure and maintainability, and introduce new functionality while ensuring the system remained stable and testable. We used agile development practices, Git-based collaboration, and UML documentation to manage our progress.

### My Work

Working as part of a five-person team, I contributed to both the design and implementation stages:
	
•	Feature Development: implemented and improved the program’s optimization logic by integrating different algorithms (Nearest Neighbor, OneOptimizer, etc.) through a new OptimizerFactory.
	
•	Design Refactoring: applied object-oriented design patterns (Factory, Template Method) to organize the codebase and reduce redundancy.
	
•	TourRequest Integration: helped redesign the TourRequest and TourOptimizer classes so they interacted more cleanly, using abstract methods for improved flexibility.
	
•	Team Coordination: collaborated through Git branches and pull requests, resolved merge conflicts, and conducted code reviews to maintain consistent style and quality.
	
•	Testing & Documentation: contributed to JUnit test suites, reviewed peer code for functionality and clarity, and helped update the UML diagrams and design summaries.

Technical Notes

•	Languages: Java
	
•	Tools: GitHub, Gradle, JUnit, UML
	
•	Concepts: Design patterns (Factory, Template Method), code modularity, agile teamwork, version control

### Reflection

This project taught me how to work on a shared, evolving codebase, where communication and structure were just as important as writing code. By coordinating across multiple team members, we learned how to build on each other’s work without conflicts, balance priorities during sprints, and deliver stable, maintainable features under real project conditions. It reinforced the value of teamwork and clear architecture in professional software engineering.


## [Project: Music Analyzer — Interactive CLI Application](https://github.com/yeahbrahh/Music-Analyzer)

This project involved developing a stateful, menu-driven command-line program in Java that analyzed music datasets stored in CSV files. Users could load a folder of files, select a dataset, and run a variety of analytical features such as Song Stats, User Similarity, User Prediction, and User Recommendation.

### My Work

I built both the core analysis logic and the interactive user interface that tied everything together.
	
•	Implemented all major features:

•	Song Stats: computed aggregate statistics for songs across users.

•	User Similarity: calculated similarity scores between user profiles.

•	User Prediction: predicted user ratings for songs based on patterns in data.

•	User Recommendation: generated personalized song suggestions based on prior inputs.

•	Created the menu system.

•	Added support for dynamic menus that listed available files and songs in lexicographical order.

•	Implemented file I/O for both reading CSV datasets and writing output results.

•	Integrated all previous features into one cohesive, user-friendly CLI.

### Technical Notes
	
•	Language: Java 20

•	Files: Cs214Project.java (main program), TestCs214Project.java (JUnit tests)

### Reflection

Building the Music Analyzer from core logic to full user interface gave me hands-on experience with modular program design. It was the first time I had to integrate multiple independent features into one smooth user experience, a challenge that strengthened both my technical skills and my ability to structure large projects clearly.


## [Project: Voice and Gaze-Controlled Maze Navigation Experiment](https://github.com/Principles-of-HCI-Group-7/maze)

This course focused on understanding how people interact with technology and how to design systems that support intuitive, accessible user experiences. My team developed a Unity-based experiment that compared two input modalities (voice commands and eye-tracking) to study their effects on user navigation and performance in a virtual maze.

### My Work
Working as part of a small team, I contributed to both the technical and design aspects of the project:
	
•	Collaborative System Design: helped plan the experiment structure, task flow, and participant interactions through team design meetings and shared documentation.
	
•	Implementation: developed major parts of the Unity application, including the maze environment, input integration, and data logging system that tracked metrics such as time, wrong turns, and completion rate.
	
•	Team Coordination: used version control and iterative feedback sessions to merge code changes, maintain consistent UI behavior, and refine experiment conditions based on group testing.
	
•	User Testing: assisted in organizing participant sessions, observing user behavior, and helping analyze quantitative and qualitative results with teammates.

### Technical Notes
	
•	Tools: Unity, C#, GazeRecorder API, CSV data logging
	
•	Focus Areas: usability testing, collaborative development, experimental design, multimodal interaction

### Reflection
This project highlighted the importance of team communication and coordination in balancing design goals with implementation needs. By working together to integrate different input systems and refine the experiment based on user feedback, we learned how collaboration enhances both technical quality and user experience. It reinforced how human-computer interaction is not only about interfaces but also about teamwork and shared problem-solving throughout the design process.

	
•	Focus Areas: refactoring, modular design, collaboration, version control, testing

### Reflection
This project taught me how to work effectively in a professional development cycle — balancing personal code style with group standards, communicating clearly about design choices, and integrating changes smoothly in a shared repository. It strengthened my ability to contribute to larger systems rather than isolated programs.
