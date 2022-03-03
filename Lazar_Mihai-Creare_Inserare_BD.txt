CREATE TABLE ADRESE(
    adresa_id NUMBER(5),
    judet VARCHAR2(50) CONSTRAINT null_judet_adr not null,
    localitate VARCHAR2(50) CONSTRAINT null_localitate_adr not null,
    cod_postal VARCHAR2(6) CONSTRAINT null_cod_postal not null,
    strada VARCHAR2(50),
    CONSTRAINT pk_adr PRIMARY KEY(adresa_id),
    CONSTRAINT unq_adresa_adr UNIQUE(judet, localitate, cod_postal, strada),
    CONSTRAINT lungime_cod_postal_adr CHECK(LENGTH(cod_postal) = 6)
);

COMMIT;


CREATE TABLE FUNCTII(
    functie_id NUMBER(5),
    denumire VARCHAR2(30) CONSTRAINT null_denumire_fun not null,
    salariu_minim NUMBER(10, 2) CONSTRAINT null_salariu_min_fun not null,
    salariu_maxim NUMBER(10, 2) CONSTRAINT null_salariu_max_fun not null,
    CONSTRAINT pk_fun PRIMARY KEY(functie_id),
    CONSTRAINT salariu_min_max_logic CHECK(salariu_minim <= salariu_maxim),
    CONSTRAINT unq_denumire_fun UNIQUE(denumire),
    CONSTRAINT salariu_min_not_negative_fun CHECK(salariu_minim > 0)
);

COMMIT;



CREATE TABLE ANGAJATI(
    angajat_id NUMBER(5),
    nume VARCHAR2(25) CONSTRAINT null_nume_ang not null,
    prenume VARCHAR2(25) CONSTRAINT null_prenume_ang not null,
    cnp VARCHAR2(13) CONSTRAINT null_cnp_ang not null,
    telefon VARCHAR2(20) CONSTRAINT null_telefon_ang not null,
    email VARCHAR2(320) CONSTRAINT null_email_ang not null,
    salariu NUMBER(10,2) CONSTRAINT null_salariu_ang not null,
    data_angajare DATE DEFAULT sysdate,
    functie_id NUMBER(5) CONSTRAINT null_functie_ang not null,
    biblioteca_id NUMBER(5) CONSTRAINT null_biblioteca_ang not null,
    CONSTRAINT pk_ang PRIMARY KEY(angajat_id),
    CONSTRAINT fk_angajati_functii_ang FOREIGN KEY(functie_id) REFERENCES FUNCTII(functie_id) ON DELETE CASCADE,
    CONSTRAINT unq_cnp_ang UNIQUE(cnp),
    CONSTRAINT unq_telefon_ang UNIQUE(telefon),
    CONSTRAINT unq_email_ang UNIQUE(email),
    CONSTRAINT salariu_logic_ang CHECK(salariu > 0),
    CONSTRAINT lungime_telefon_ang CHECK(LENGTH(telefon) >= 10)
);

COMMIT;



CREATE TABLE BIBLIOTECI(
    biblioteca_id NUMBER(5),
    denumire VARCHAR2(30) CONSTRAINT null_denumire_bibl not null,
    telefon VARCHAR2(20) CONSTRAINT null_telefon_bibl not null,
    manager_id NUMBER(5),
    adresa_id NUMBER(5) CONSTRAINT null_adresa_bibl not null,
    CONSTRAINT pk_bibl PRIMARY KEY(biblioteca_id),
    CONSTRAINT fk_manager_biblioteca_bibl FOREIGN KEY(manager_id) REFERENCES ANGAJATI(angajat_id) ON DELETE SET NULL,
    CONSTRAINT fk_adresa_biblioteca_bibl FOREIGN KEY(adresa_id) REFERENCES ADRESE(adresa_id) ON DELETE CASCADE,
    CONSTRAINT unq_denumire_bibl UNIQUE(denumire),
    CONSTRAINT unq_telefon_bibl UNIQUE(telefon),
    CONSTRAINT unq_manager_bibl UNIQUE(manager_id),
    CONSTRAINT lungime_telefon_bibl CHECK(LENGTH(telefon) >= 10)
);

ALTER TABLE ANGAJATI
ADD CONSTRAINT fk_angajati_biblioteci_ang FOREIGN KEY(biblioteca_id) REFERENCES BIBLIOTECI(biblioteca_id) ON DELETE CASCADE;

COMMIT;


CREATE TABLE ADRESA_ANG(
    angajat_id NUMBER(5),
    adresa_id NUMBER(5),
    CONSTRAINT pk_adrang PRIMARY KEY(angajat_id, adresa_id),
    CONSTRAINT fk_angajat_adrang FOREIGN KEY(angajat_id) REFERENCES ANGAJATI(angajat_id) ON DELETE CASCADE,
    CONSTRAINT fk_adresa_adrang FOREIGN KEY(adresa_id) REFERENCES ADRESE(adresa_id) ON DELETE CASCADE
);
COMMIT;


CREATE TABLE AUTORI(
    autor_id NUMBER(5),
    nume VARCHAR2(25) CONSTRAINT null_nume_autor not null,
    prenume VARCHAR2(25) CONSTRAINT null_prenume_autor not null,
    CONSTRAINT pk_aut PRIMARY KEY(autor_id),
    CONSTRAINT unq_nume_prenume_autor UNIQUE(nume, prenume)
);

COMMIT;

CREATE TABLE EDITURI(
editura_id NUMBER(5), 
denumire VARCHAR2(30) CONSTRAINT null_denumire_edituri not null, 
telefon VARCHAR2(20) CONSTRAINT null_telefon_edituri not null,
email VARCHAR2(320) CONSTRAINT null_email_edituri not null, 
adresa_id NUMBER(5) CONSTRAINT null_adresa_edituri not null,
CONSTRAINT pk_edituri PRIMARY KEY(editura_id),
CONSTRAINT fk_adresa_edituri FOREIGN KEY(adresa_id) REFERENCES ADRESE(adresa_id) ON DELETE CASCADE,
CONSTRAINT unq_denumire_edituri UNIQUE(denumire),
CONSTRAINT unq_telefon_edituri UNIQUE(telefon),
CONSTRAINT unq_email_edituri UNIQUE(email),
CONSTRAINT lungime_telefon_edituri CHECK(LENGTH(telefon) >= 10)
);

COMMIT;



CREATE TABLE CARTI(
    carte_id NUMBER(5),
    titlu VARCHAR2(50) CONSTRAINT null_titlu_car not null,
    editura_id NUMBER(5) CONSTRAINT null_editura_car not null,
    an_aparitie NUMBER(5) CONSTRAINT null_an_car not null,
    autor_id NUMBER(5) CONSTRAINT null_autor_car not null,
    tip_carte VARCHAR2(25) CONSTRAINT null_tip_car not null,
    CONSTRAINT pk_car PRIMARY KEY(carte_id),
    CONSTRAINT fk_autor_carte_car FOREIGN KEY(autor_id) REFERENCES AUTORI(autor_id) ON DELETE CASCADE,
    CONSTRAINT fk_editura_carte_car FOREIGN KEY(editura_id) REFERENCES EDITURI(editura_id) ON DELETE CASCADE
);

COMMIT;

CREATE TABLE CITITORI(
    cititor_id NUMBER(5), 
    nume VARCHAR2(25) CONSTRAINT null_nume_cit not null, 
    prenume VARCHAR2(25) CONSTRAINT null_prenume_cit not null, 
    cnp VARCHAR2(13) CONSTRAINT null_cnp_cit not null, 
    telefon VARCHAR2(20) CONSTRAINT null_telefon_cit not null, 
    email VARCHAR2(320) CONSTRAINT null_email_cit not null,
    CONSTRAINT pk_cit PRIMARY KEY(cititor_id),
    CONSTRAINT unq_cnp_cit UNIQUE(cnp),
    CONSTRAINT unq_telefon_cit UNIQUE(telefon),
    CONSTRAINT unq_email_cit UNIQUE(email),
    CONSTRAINT lungime_telefon_cit CHECK(LENGTH(telefon) >= 10)
);

COMMIT;




CREATE TABLE ADRESA_CIT(
    cititor_id NUMBER(5),
    adresa_id NUMBER(5),
    CONSTRAINT pk_adrcit PRIMARY KEY(cititor_id, adresa_id),
    CONSTRAINT fk_cititor_adrcit FOREIGN KEY(cititor_id) REFERENCES CITITORI(cititor_id) ON DELETE CASCADE,
    CONSTRAINT fk_adresa_adrcit FOREIGN KEY(adresa_id) REFERENCES ADRESE(adresa_id) ON DELETE CASCADE
);


COMMIT;


CREATE TABLE PERMIS_DE_INTRARE(
permis_id NUMBER(5), 
anul_inscrieri NUMBER(5) DEFAULT TO_CHAR(sysdate, 'YYYY'), 
cititor_id NUMBER(5) CONSTRAINT null_cititor_permis not null, 
biblioteca_id NUMBER(5) CONSTRAINT null_biblioteca_permis not null,
CONSTRAINT pk_permis PRIMARY KEY(permis_id),
CONSTRAINT fk_cititor_permis FOREIGN KEY(cititor_id) REFERENCES CITITORI(cititor_id) ON DELETE CASCADE,
CONSTRAINT fk_biblioteca_permis FOREIGN KEY(biblioteca_id) REFERENCES BIBLIOTECI(biblioteca_id) ON DELETE CASCADE,
CONSTRAINT unq_biblioteca_cititor_permis UNIQUE(cititor_id, biblioteca_id)
);

COMMIT;


CREATE TABLE FISA_DE_LECTURA(
    fisa_id NUMBER(5), 
    cititor_id NUMBER(5) CONSTRAINT null_cititor_fisa not null, 
    carte_id NUMBER(5) CONSTRAINT null_carte_fisa not null,
    biblioteca_id NUMBER(5) CONSTRAINT null_biblioteca_fisa not null, 
    permis_id NUMBER(5) CONSTRAINT null_permis_fisa not null, 
    data_imprumut DATE DEFAULT sysdate, 
    data_restituire DATE DEFAULT sysdate,
    CONSTRAINT pk_fisa PRIMARY KEY(fisa_id),
    CONSTRAINT fk_cititor_fisa FOREIGN KEY(cititor_id) REFERENCES CITITORI(cititor_id) ON DELETE CASCADE,
    CONSTRAINT fk_carte_fisa FOREIGN KEY(carte_id) REFERENCES CARTI(carte_id) ON DELETE CASCADE,
    CONSTRAINT fk_biblioteca_fisa FOREIGN KEY(biblioteca_id) REFERENCES BIBLIOTECI(biblioteca_id) ON DELETE CASCADE,
    CONSTRAINT fk_permis_fisa FOREIGN KEY(permis_id) REFERENCES PERMIS_DE_INTRARE(permis_id) ON DELETE CASCADE,
    CONSTRAINT data_logic_fisa CHECK(data_imprumut <= data_restituire),
    CONSTRAINT unq_carte_data_imprumut_fisa UNIQUE(cititor_id, carte_id, data_imprumut)
);

COMMIT;



CREATE TABLE FURNIZORI(
furnizor_id NUMBER(5),
denumire VARCHAR2(30) CONSTRAINT null_denumire_furn not null,
telefon VARCHAR2(20) CONSTRAINT null_telefon_furn not null,
email VARCHAR2(320) CONSTRAINT null_email_furn not null,
adresa_id NUMBER(5) CONSTRAINT null_adresa_furn not null,
CONSTRAINT pk_furn PRIMARY KEY(furnizor_id),
CONSTRAINT fk_adresa_furn FOREIGN KEY(adresa_id) REFERENCES ADRESE(adresa_id) ON DELETE CASCADE,
CONSTRAINT unq_denumire_furn UNIQUE(denumire),
CONSTRAINT unq_telefon_furn UNIQUE(telefon),
CONSTRAINT unq_email_furn UNIQUE(email),
CONSTRAINT lungime_telefon_furn CHECK(LENGTH(telefon) >= 10)
);


COMMIT;





CREATE TABLE LUCREAZA(
editura_id NUMBER(5),
furnizor_id NUMBER(5),
CONSTRAINT pk_lucreaza PRIMARY KEY(editura_id, furnizor_id),
CONSTRAINT fk_editura_lucreaza FOREIGN KEY(editura_id) REFERENCES EDITURI(editura_id) ON DELETE CASCADE,
CONSTRAINT fk_furnizor_lucreaza FOREIGN KEY(furnizor_id) REFERENCES FURNIZORI(furnizor_id) ON DELETE CASCADE
);

COMMIT;

CREATE TABLE COMENZI(
comanda_id NUMBER(5),
furnizor_id NUMBER(5) CONSTRAINT null_furnizor_comenzi not null,
carte_id NUMBER(5) CONSTRAINT null_carte_comenzi not null,
biblioteca_id NUMBER(5) CONSTRAINT null_biblioteca_comenzi not null,
nr_exemplare NUMBER(10) CONSTRAINT null_nr_exemplare_comenzi not null,
pret_comanda NUMBER(10, 2) CONSTRAINT null_pret_comenzi not null,
CONSTRAINT pk_comenzi PRIMARY KEY(comanda_id),
CONSTRAINT fk_furnizor_comenzi FOREIGN KEY(furnizor_id) REFERENCES FURNIZORI(furnizor_id) ON DELETE CASCADE,
CONSTRAINT fk_carte_comenzi FOREIGN KEY(carte_id) REFERENCES CARTI(carte_id) ON DELETE CASCADE,
CONSTRAINT fk_biblioteca_comenzi FOREIGN KEY(biblioteca_id) REFERENCES BIBLIOTECI(biblioteca_id) ON DELETE CASCADE,
CONSTRAINT nr_exemplare_logic CHECK(nr_exemplare > 0),
CONSTRAINT pret_logic CHECK(pret_comanda >= 0)
);

COMMIT;

CREATE TABLE SUBTIPURI(
subtip_id NUMBER(5),
denumire VARCHAR2(30) CONSTRAINT null_denumire_subtipuri not null,
CONSTRAINT pk_subtipuri PRIMARY KEY(subtip_id),
CONSTRAINT unq_denumire_subtipuri UNIQUE(denumire)
);


COMMIT;


CREATE TABLE CATALOGARE(
carte_id NUMBER(5),
subtip_id NUMBER(5),
CONSTRAINT pk_catalog PRIMARY KEY(carte_id, subtip_id),
CONSTRAINT fk_carte_catalog FOREIGN KEY(carte_id) REFERENCES CARTI(carte_id) ON DELETE CASCADE,
CONSTRAINT fk_subtip_catalog FOREIGN KEY(subtip_id) REFERENCES SUBTIPURI(subtip_id) ON DELETE CASCADE
);

COMMIT;


CREATE SEQUENCE seq_adrese
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_angajati
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_autori
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_biblioteci
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_carti
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_cititori
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_comenzi
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_edituri
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_fisa_de_lectura
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_functii
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_furnizori
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_permis_de_intrare
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

CREATE SEQUENCE seq_subtipuri
START WITH 1
INCREMENT BY 1
NOCYCLE
NOCACHE;

COMMIT;


INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Alba', 'Sebe?', '515800', 'Bistrei');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Olt', 'Brastavă?u', '237045', 'Bujorului');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Cluj', 'Cluj-Napoca', '400417', 'Unirii');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Bihor', 'Oradea', '410087', 'Bacăului');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Sibiu', 'Turnu Ro?u', '557285', 'Olte?');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Dolj', 'Popoveni', '200003', 'Brăila');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Maramure?', 'Baia Mare', '430301', 'Vasile Alecsandri');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Gorj', 'Târgu Jiu', '210101', 'Aleea Livezi');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Satu Mare', 'Satu Mare', '440141', 'Dariu Pop');
INSERT INTO ADRESE(adresa_id, judet, localitate, cod_postal, strada)
VALUES (SEQ_ADRESE.nextval, 'Ia?i', 'Ia?i', '700341', 'Vasile Lupu');


COMMIT;




INSERT INTO FUNCTII(functie_id, denumire, salariu_minim, salariu_maxim)
VALUES (SEQ_FUNCTII.nextval, 'Bibliotecar', 6000, 12000);
INSERT INTO FUNCTII(functie_id, denumire, salariu_minim, salariu_maxim)
VALUES (SEQ_FUNCTII.nextval, 'Pază', 1000, 5000);
INSERT INTO FUNCTII(functie_id, denumire, salariu_minim, salariu_maxim)
VALUES (SEQ_FUNCTII.nextval, 'Catalogare', 3000, 9000);
INSERT INTO FUNCTII(functie_id, denumire, salariu_minim, salariu_maxim)
VALUES (SEQ_FUNCTII.nextval, 'Consilier', 4000, 10000);
INSERT INTO FUNCTII(functie_id, denumire, salariu_minim, salariu_maxim)
VALUES (SEQ_FUNCTII.nextval, 'Marketing', 6000, 8000);

COMMIT;



INSERT INTO BIBLIOTECI(biblioteca_id, denumire, telefon, manager_id, adresa_id)
VALUES (SEQ_BIBLIOTECI.nextval, 'Biblioteca Ro?ie', '0773.245.664', null, 5);
INSERT INTO BIBLIOTECI(biblioteca_id, denumire, telefon, manager_id, adresa_id)
VALUES (SEQ_BIBLIOTECI.nextval, 'Biblioteca De Aur', '0773.236.918', null, 9);
INSERT INTO BIBLIOTECI(biblioteca_id, denumire, telefon, manager_id, adresa_id)
VALUES (SEQ_BIBLIOTECI.nextval, 'Biblioteca Mihai Eminescu', '0772.019.467', null, 2);
INSERT INTO BIBLIOTECI(biblioteca_id, denumire, telefon, manager_id, adresa_id)
VALUES (SEQ_BIBLIOTECI.nextval, 'Biblioteca Bacovia', '0772.162.250', null, 4);
INSERT INTO BIBLIOTECI(biblioteca_id, denumire, telefon, manager_id, adresa_id)
VALUES (SEQ_BIBLIOTECI.nextval, 'Biblioteca Ion Creangă', '0772.309.337', null, 5);

COMMIT;

INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (SEQ_ANGAJATI.nextval, 'Petran', 'Teodor', '2476281827462',
'0772.124.547', 'PetranTeodor@gmail.com', 10000, '27/01/1944', 1, 4);

INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (SEQ_ANGAJATI.nextval, 'Funar', 'Ioan', '1277364727462',
'0772.754.124', 'IoanFunar@gmail.com', 7000, '20/09/1985', 4, 3);

INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (SEQ_ANGAJATI.nextval, 'David', 'Alexandru', '1007264727890',
'0772.493.992', 'DavidAlexandru@gmail.com', 6500, '08/06/1999', 5, 5);

INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (SEQ_ANGAJATI.nextval, 'Ungur', 'Lucia', '1009805727890',
'0772.586.021', 'UngurLucia@gmail.com', 6000, '07/12/1987', 3, 1);

INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (SEQ_ANGAJATI.nextval, 'Cojocaru', 'Virgil', '9992264773829',
'0772.235.754', 'CojocaruVirgil@gmail.com', 2500, '10/05/1980', 2, 2);

COMMIT;

UPDATE biblioteci
SET manager_id = biblioteca_id
where MOD(biblioteca_id, 2) = 0;

COMMIT;


INSERT INTO CITITORI(cititor_id, nume, prenume, cnp, telefon, email)
VALUES (SEQ_CITITORI.nextval, 'Lupu', 'Ligia', '1234567890123', '0772.938.856',
'LigiaLupu@gmail.com');
INSERT INTO CITITORI(cititor_id, nume, prenume, cnp, telefon, email)
VALUES (SEQ_CITITORI.nextval, 'Văduva', 'Adela', '1234554390123', '0772.845.584',
'VaduvaAdela@gmail.com');
INSERT INTO CITITORI(cititor_id, nume, prenume, cnp, telefon, email)
VALUES (SEQ_CITITORI.nextval, 'Vasile', 'Dorian', '1784567890123', '0772.125.797',
'DorianVasile@gmail.com');
INSERT INTO CITITORI(cititor_id, nume, prenume, cnp, telefon, email)
VALUES (SEQ_CITITORI.nextval, 'Solomon', 'Ivan', '1237777790123', '0772.999.856',
'SolomonIvan@gmail.com');
INSERT INTO CITITORI(cititor_id, nume, prenume, cnp, telefon, email)
VALUES (SEQ_CITITORI.nextval, 'Vasile', 'Andrada', '1876567658423', '0772.788.100',
'AndradaVasile@gmail.com');

COMMIT;

INSERT INTO AUTORI(autor_id, nume, prenume)
VALUES(SEQ_AUTORI.nextval, 'Morris', 'Nikolina');
INSERT INTO AUTORI(autor_id, nume, prenume)
VALUES(SEQ_AUTORI.nextval, 'Tatham', 'Aishwarya');
INSERT INTO AUTORI(autor_id, nume, prenume)
VALUES(SEQ_AUTORI.nextval, 'Brambilla', 'Shprintze');
INSERT INTO AUTORI(autor_id, nume, prenume)
VALUES(SEQ_AUTORI.nextval, 'Zając', 'Hayder');
INSERT INTO AUTORI(autor_id, nume, prenume)
VALUES(SEQ_AUTORI.nextval, 'Modugno', 'Loukianos');

COMMIT;

INSERT INTO EDITURI(editura_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_EDITURI.nextval, 'Editura Roll', '0773.244.533', 'EdituraRoll@gmail.com', 5);
INSERT INTO EDITURI(editura_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_EDITURI.nextval, 'Editura Sketch', '0770.214.654', 'EdituraSketch@gmail.com', 1);
INSERT INTO EDITURI(editura_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_EDITURI.nextval, 'Editura Lot', '0770.252.647', 'EdituraLot@gmail.com', 2);
INSERT INTO EDITURI(editura_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_EDITURI.nextval, 'Editura Thesis', '0770.241.447', 'EdituraThesis@gmail.com', 6);
INSERT INTO EDITURI(editura_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_EDITURI.nextval, 'Editura Arena', '0774.123.347', 'EdituraArena@gmail.com', 10);

COMMIT;


INSERT INTO FURNIZORI(furnizor_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_FURNIZORI.nextval, 'Turkey Cucumber', '0774.004.122', 'TurkeyCucumber@gmail.com', 7);
INSERT INTO FURNIZORI(furnizor_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_FURNIZORI.nextval, 'Tradition Spill', '0774.353.689', 'TraditionSpill@gmail.com', 1);
INSERT INTO FURNIZORI(furnizor_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_FURNIZORI.nextval, 'Mobile Feather', '0774.121.224', 'MobileFeather@gmail.com', 2);
INSERT INTO FURNIZORI(furnizor_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_FURNIZORI.nextval, 'Gallery Lion', '0774.233.646', 'GalleryLion@gmail.com', 8);
INSERT INTO FURNIZORI(furnizor_id, denumire, telefon, email, adresa_id)
VALUES (SEQ_FURNIZORI.nextval, 'Gold Page', '0774.241.366', 'GoldPage@gmail.com', 5);

COMMIT;

INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Animale');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Matematcă');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Fizică');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Muzică');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Artă');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Informatică');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Horror');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Drama');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Ac?iune');
INSERT INTO SUBTIPURI(subtip_id, denumire)
VALUES (SEQ_SUBTIPURI.nextval, 'Poli?iste');

COMMIT;

INSERT INTO CARTI(carte_id, titlu, editura_id, an_aparitie, autor_id, tip_carte)
VALUES (SEQ_CARTI.nextval, 'Cunoscutul', 5, 1800, 2, '?tiin?ific');
INSERT INTO CARTI(carte_id, titlu, editura_id, an_aparitie, autor_id, tip_carte)
VALUES (SEQ_CARTI.nextval, 'Animale terestre', 1, 1950, 5, 'Pentru copii');
INSERT INTO CARTI(carte_id, titlu, editura_id, an_aparitie, autor_id, tip_carte)
VALUES (SEQ_CARTI.nextval, 'Aventurile lui Ro?u', 4, 1877, 3, 'Pove?ti');
INSERT INTO CARTI(carte_id, titlu, editura_id, an_aparitie, autor_id, tip_carte)
VALUES (SEQ_CARTI.nextval, 'Matematică pentru începători', 2, 2000, 1, '?tiin?ific');
INSERT INTO CARTI(carte_id, titlu, editura_id, an_aparitie, autor_id, tip_carte)
VALUES (SEQ_CARTI.nextval, 'Poeziile naturi', 3, 1899, 4, 'Poezii');

COMMIT;



INSERT INTO COMENZI(comanda_id, furnizor_id, carte_id, biblioteca_id, nr_exemplare, pret_comanda)
VALUES(SEQ_COMENZI.nextval, 4, 1, 3, 10, 500);
INSERT INTO COMENZI(comanda_id, furnizor_id, carte_id, biblioteca_id, nr_exemplare, pret_comanda)
VALUES(SEQ_COMENZI.nextval, 1, 4, 5, 25, 2000);
INSERT INTO COMENZI(comanda_id, furnizor_id, carte_id, biblioteca_id, nr_exemplare, pret_comanda)
VALUES(SEQ_COMENZI.nextval, 3, 2, 2, 20, 1500);
INSERT INTO COMENZI(comanda_id, furnizor_id, carte_id, biblioteca_id, nr_exemplare, pret_comanda)
VALUES(SEQ_COMENZI.nextval, 5, 3, 4, 15, 1800);
INSERT INTO COMENZI(comanda_id, furnizor_id, carte_id, biblioteca_id, nr_exemplare, pret_comanda)
VALUES(SEQ_COMENZI.nextval, 2, 5, 1, 30, 4000);

COMMIT;


INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2000, 1, 3);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2010, 2, 5);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2001, 5, 2);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2018, 3, 1);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 1990, 4, 4);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2011, 3, 5);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2007, 4, 3);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2021, 2, 2);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2014, 1, 4);
INSERT INTO PERMIS_DE_INTRARE(permis_id, anul_inscrieri, cititor_id, biblioteca_id)
VALUES (SEQ_PERMIS_DE_INTRARE.nextval, 2009, 1, 5);

COMMIT;

INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(1, 2);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(4, 4);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(2, 3);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(5, 1);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(3, 5);

INSERT INTO LUCREAZA(editura_id, furnizor_id)
SELECT ca.editura_id, co.furnizor_id
FROM carti ca, comenzi co
WHERE ca.carte_id = co.carte_id
AND (ca.editura_id, co.furnizor_id) not in (
SELECT editura_id, furnizor_id
FROM lucreaza
);

COMMIT;


INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 1, 1, 3, 1, '10/05/1980', '30/05/1980');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 2, 2, 5, 4, '01/09/1990', '11/09/1990');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 3, 5, 2, 2, '05/11/2000', '15/11/2000');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 4, 3, 1, 5, '29/09/2005', '06/10/2005');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 5, 4, 4, 3, '03/01/2010', '13/01/2010');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 6, 3, 5, 4, '09/11/2008', '19/11/2008');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 7, 4, 3, 1, '5/11/2000', '5/11/2000');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 8, 2, 2, 2, '01/09/1990', '11/09/1990');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 9, 1, 4, 3, '29/09/2005', '06/10/2005');
INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(SEQ_FISA_DE_LECTURA.nextval, 10, 1, 5, 4, '10/05/1980', '30/05/1980');


COMMIT;



INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(1, 2);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(1, 3);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(2, 1);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(2, 5);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(2, 4);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(3, 7);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(3, 8);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(3, 9);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(3, 10);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(4, 2);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(5, 1);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(5, 5);
INSERT INTO CATALOGARE(carte_id, subtip_id)
VALUES(5, 4);

COMMIT;


INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(1, 4);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(1, 9);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(2, 10);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(2, 3);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(3, 6);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(3, 7);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(4, 9);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(4, 1);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(5, 10);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(5, 8);

COMMIT;

INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(1, 7);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(1, 10);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(2, 1);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(2, 4);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(3, 6);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(3, 3);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(4, 9);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(4, 1);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(5, 8);
INSERT INTO ADRESA_CIT(cititor_id, adresa_id)
VALUES(5, 2);

COMMIT;

