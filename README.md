Student Management

To add students and send out email invites to a course (say called COURSE-A) follow these steps;

    Get an sudo level account from RSB IT for the teaching server
    Install ansible on your workstation, follow the instructions here
    Clone the ansible code repository https://github.com/RSB-IT/bio-courses1-student-mgt to your local system. If not sure, google how to do that with git.Or ask RSB IT for help.
    Change into the folder/directory of the local copy of the repo
    Get a list of the students,This should be as a CSV, saved as userlist_COURSE-A.csv under this directory. The columns are email,role (see template), role is either student or staff. It is converted to the format Ansible needs using a script (see below). NB: They must be valid emails. Also, you can add a column uid and that will be their username, otherwise it is derived from their email as username_firstbitofdomain, e.g. u1234567@anu.edu.au → u1234567_anu.
        For example the first few lines would look like:

        email,role
        cazodi@svi.edu.au,student
        philipp.bayer@uwa.edu.au,student
        cecilia.deng@plantandfood.co.nz,student

    Now convert it to the format needed by the ansible code,
        ./scripts/users2yml -o vars/userlist_COURSE-A.yml userlist_COURSE-A.csv
    Copy the example create-email students code for your course
        cp example.dousers.yml dousers_COURSE-A.yml
    Edit the dousers course yml file and edit the welcome message (the text under user_email_body), and make the vars_files setting points to vars/userlist_COURSE-A.yml. Also edit the tags setting and add COURSE-A.
    Run the create-email students code with the following command;
        ansible-playbook -i hosts dousers_COURSE-A.yml -u (your username)
        students will now be created and emailed with the info they need. You can add new students at anytime and redo steps 5,8. It will only email the new students not the existing ones.

You do not need to remove students, this will be done automatically at the end of each semester
