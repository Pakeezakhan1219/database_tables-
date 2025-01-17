# database_tables-
My Database project

The project focused on improving the structure of a database initially stored in a single table, "university_professor", which included professors' details, universities, organizations, and affiliations. This single table caused redundancy, inefficiency, and difficulty in managing data. To solve this, the data was split into four related tables: Professors (personal details), Universities (university info), Organizations (organization details), and Affiliations (linking professors to organizations with their roles). This approach reduced redundancy, improved data consistency, made queries easier, and ensured the database could handle growth efficiently.

select *
from university_professor;
 
CREATE TABLE professors (
 professor_id SERIAL PRIMARY KEY ,
   firstname VARCHAR(255),
    lastname VARCHAR(255)
);
 
CREATE TABLE universities (
    university_id SERIAL PRIMARY KEY,
 university VARCHAR(255),
    university_shortname VARCHAR(50),
    university_city VARCHAR(255),
professor_id INT REFERENCES professors(   professor_id)
);
 
CREATE TABLE organizations (
   organization_id SERIAL PRIMARY KEY,
    organization VARCHAR(255),
   organization_sector VARCHAR(255)
);
 
CREATE TABLE affiliations (
    affiliation_id SERIAL PRIMARY KEY,
    professor_id INT REFERENCES professors (professor_id),
  organization_id INT REFERENCES organizations(organization_id),
 function VARCHAR (255)
);
 
INSERT INTO professors (  firstname, lastname  )
  SELECT DISTINCT firstname, lastname
 FROM university_professor;
 
     INSERT INTO universities  (  university   , university_shortname,     university_city )
SELECT DISTINCT university, university_shortname, university_city
FROM university_professor;
 
INSERT INTO organizations (organization, organization_sector)
SELECT DISTINCT organization, organization_sector
   FROM university_professor;
 
INSERT INTO affiliations (professor_id, organization_id, function)
SELECT 
    p.professor_id,  
	 o.organization_id, 
    mt.function
FROM  university_professor mt
JOIN professors p
 ON mt.firstname = p.firstname AND mt.lastname = p.lastname
JOIN organizations o
 ON mt.organization = o.organization AND mt.organization_sector = o.organization_sector;

ALTER TABLE university_professor
RENAME TO university_professors;

ALTER TABLE universities
DROP CONSTRAINT universities_professor_id_fkey;

ALTER TABLE universities 
DROP COLUMN professor_id ;

ALTER TABLE professors
ADD COLUMN university_id INTEGER;

ALTER TABLE professors
ADD CONSTRAINT fk_university FOREIGN KEY (university_id) REFERENCES universities (university_id);

ALTER TABLE professors
ALTER COLUMN firstname SET NOT NULL,
ALTER COLUMN lastname SET NOT NULL  ;

ALTER TABLE universities
ALTER COLUMN university SET NOT NULL,
ALTER COLUMN university_shortname SET NOT NULL,
ALTER COLUMN university_city SET NOT NULL;

ALTER TABLE organizations
ALTER COLUMN organization SET NOT NULL,
ALTER COLUMN organization_sector SET NOT NULL ;

ALTER TABLE affiliations
ALTER COLUMN professor_id SET NOT NULL,
ALTER COLUMN organization_id SET NOT NULL ;
