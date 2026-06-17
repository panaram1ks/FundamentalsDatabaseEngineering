https://docs.google.com/document/d/1fgo1hfkFp4L_Uz01VT4y9lMXk8s7K53nwT6GcSN_ouU/edit?hl=RU&pli=1&tab=t.0

# присоединиться к контейнеру
docker exec -it <container_name> bash

# зайти в терминал postgres внутри контейнера
psql -U postgres
# create table into postgres
create table grades_org (id serial not null, g int not null);
#insert 10_000_000 random entries to table
insert into grades_org(g) select floor(random() * 100) from generate_series(0, 10000000);

# create index
create index grades_org_index on grades_org(g);

# structure table  -> describe the table
\d grades_org;

# Partitioned Tabel
create table grades_parts (id serial not null, g int not null) partition by range(g);

# Create and Attach Partitioned Tables
    create table g0035 (like grades_parts include indexes); # 0->35
    create table g3560 (like grades_parts including indexes); # 35->60
    create table g6080 (like grades_parts including indexes); # 60->80
    create table g80100 (like grades_parts including indexes); # 80->100

alter table grades_parts attach partition g0035 for values from (0) to (35);
alter table grades_parts attach partition g3560 for values from (35) to (60);
alter table grades_parts attach partition g6080 for values from (60) to (80);
alter table grades_parts attach partition g80100 for values from (80) to (100);

# Populate the Partitions and Create Indexes
    insert into grades_parts select * from grades_org;

# Populate the Partitions and Create Indexes
    explain analyze select max(g) from grades_parts;
    create index g_index on grades_parts(g);

# Class Project - Querying and Checking the Size of Partitions
