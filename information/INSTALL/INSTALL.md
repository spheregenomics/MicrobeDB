INSTALL MicrobeDB
=
These are the basic guidelines to getting MicrobeDB set up locally on your machine

##Requirements##
You will need to install the following software BEFORE proceeding with the MicrobeDB installation below. 

* MySQL (see below)
* Perl
* Perl Modules (available from CPAN)
    * BioPerl
    * DBI
    * DBD::mysql
    * Parallel::ForkManager
    * Log::Log4perl
    * Sys::CPU

##Install MySQL##

####MAC OSX: Install MySQL####
1. Visit http://www.mysql.com/downloads/mysql/ and download appropriate package.

2. Install both the MySQL-your_version.pkg and MySQL StartupItem.pkg (so the mysql server starts every time the computer starts). 

3. Start the MySQL server by restarting your computer or running the following command from a shell:

        sudo /Library/StartupItems/MySQLCOM/MySQLCOM start

4. Add the "mysql" command to your "Path". From a shell type:
    
        sudo pico /etc/paths

    * Then add the following line to the end of the file:

        /usr/local/mysql/bin

    * Save the file.

####MAC OSX: Change the MySQL system setting 'max_allowed_packet'####

1. Copy a config file into the proper location

        sudo cp /usr/local/mysql/support-files/my-large.cnf /etc/my.cnf

2. Open up an editor on the config file

        sudo pico /etc/my.cnf

    * Find and change the following line in the config file:

        * From:

                max_allowed_packet = 1M

        * To:

                max_allowed_packet = 64M

3. Restart the MySQL server:

        sudo /Library/StartupItems/MySQLCOM/MySQLCOM restart

4. Skip to "Setup MicrobeDB database"

####Ubuntu: Install MySQL####
* Follow directions here: https://help.ubuntu.com/11.10/serverguide/C/mysql.html

####Ubuntu: Change the MySQL system setting 'max_allowed_packet'####

* Edit the MySQL configuration file 'my.cnf' usually located at /etc/mysql/my.cnf 

* Find 'max_allowed_packet' and change the line to this:

         max_allowed_packet  = 64M

* Restart the MySQL server 
  
         sudo /etc/init.d/mysql restart

##Setup MicrobeDB database##
Note: Depending on how you have MySQL installed, you may not need to provide a password for the "root" account. If so just remove the "-p" from the following statements.  

1. Create a user to access the database (Note: we use the username 'microbedb', but this can be any username).

        mysql -u root -p -e "CREATE USER 'microbedb'@'localhost' IDENTIFITED BY 'some_password'"

2. Give the user access to the microbedb database

        mysql -u root -p -e "GRANT ALL PRIVILEGES ON microbedb.* to 'microbedb'@'localhost'"

2. Create the microbedb database

        mysql -u root -p -e "CREATE DATABASE microbedb"

3. Load the microbedb table structures

        mysql -u root -p microbedb < microbedb_schema.sql

4. Create or confirm your MySQL login config file ~/.my.cnf

        emacs $HOME/.my.cnf

        [client]

        host=localhost

        user=microbedb (or whatever username you use)

        password=some_password

5. Protect your MySQL config file

        chmod 600 $HOME/.my.cnf

6. Add MicrobeDB to your perl PATH. You can do this by putting the MicrobeDB folder in the same location as other perl modules or by adding the path to MicrobeDB to your $PERL5LIB environment variable

That is it! MicrobeDB is now installed on your computer.

##Running MicrobeDB for the first time##

* To use MicrobeDB you will have to download and load a new version of genome files. 
* To do this run the following command (this will take a while to run):

        ./scripts/download_load_and_delete_old_version.pl -d /your_absolute_path/shared_genomes_directory

* Note: If there are problems at any stage in the update you can run parts of the pipeline manually (see scripts directory).

        1. download_version.pl
        2. unpack_version.pl
        3. load_version.pl

* Use -h option to get help for any of the scripts.

### Loading non-NCBI genomes (optional) ###
* Each of your personal genomes should have it's own directory named according to the name of the species. 

* Each genome directory must contain at least 1 genbank file (.gbk). 

* Multiple contigs/chromosomes will be loaded if there are seperate entries in the same genbank file or if there are multiple genbank files in the same directory. 

* You should place all of your genome directories into a single directory (e.g. 'my_unpublished_genomes')

* To save storage space your files may be gzipped (e.g. MicrobeDB will load/read directly from gzipped files). 

* These genomes are loaded using ./load_version.pl with the --custom option. These genomes are always given version id '0' and will never be automatically deleted on updates. 

* For example your file structure should look like this:

    * unpublished_genomes
        * Pseudomonas_aeruginosa_LESB58
            * Pseudomonas_aeruginoas_LESB58.gbk
        * another_genome_name
            * another_genome_name.gbk
            * another_genome_name_plasmid.gbk
        * yet_another_genome
            * yet_another_genome.gbk.gz

* To load all of these genomes at once:
  
        ./scripts/load_version.pl --custom -d /your_absolute_path/unpublished_genomes

* You can add a single genome later with:

        ./scripts/load_genome.pl -d /your_absolute_path/unpublished_genomes/still_yet_another_genome

##Questions/Comments##
* Contact: Morgan Langille
* Email: morgan.g.i.langille@gmail.com