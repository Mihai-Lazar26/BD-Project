/* Afisati datele despre cartile care au fost citite de un
cititor care are litera 'u' in numele lui, au un permis facut dupa anul 2010,
titlul carti constine litera 'p' si afisati daca cartea a fost scrisa de Modugno*/

select c.titlu, c.tip_carte, su.denumire subtip,
decode(a.nume, 'Modugno', 'Este scrisa de Modugno', 'Nu este scrisa de Modugno') as scrisa_de_moudgno
from carti c, autori a, catalogare ca, subtipuri su
where c.carte_id = ca.carte_id
and ca.subtip_id = su.subtip_id
and c.autor_id = a.autor_id
and c.carte_id in (
                    select fisa.carte_id
                    from fisa_de_lectura fisa, cititori cit, permis_de_intrare perm
                    where fisa.cititor_id = cit.cititor_id
                    and fisa.permis_id = perm.permis_id
                    and perm.anul_inscrieri >= 2010
                    and lower(cit.nume) like '%u%')
and lower(c.tip_carte) like '%p%'
order by 1;

/* Afisati numele, prenume si titlul carti 
pentru cititori care au citit o carte de la editura Sketch 
scrisa de autorul cu numele Morris*/

select distinct cit.nume, cit.prenume, ca.titlu
from cititori cit, fisa_de_lectura fisa, carti ca
where cit.cititor_id = fisa.cititor_id
and ca.carte_id = fisa.carte_id
and exists (
            select 1
            from carti c, autori a, edituri e
            where fisa.carte_id = carte_id
            and c.autor_id = a.autor_id
            and e.editura_id = ca.editura_id
            and lower(e.denumire) like '%sketch%'
            and lower(a.nume) like '%morris%');



/* Sa se afiseze carteile citite de, cititorul care a citit cel mai mult 
si acum cati ani si cate luni au fost imprumutate ele */



select c.nume, c.prenume, ca.titlu, concat('Imprumutata de el acum ',
to_char(sysdate, 'YYYY') - to_char(fisa.data_imprumut, 'YYYY')) || ' de ani.' ani,
'Imprumutat de el acum '||FLOOR(MONTHS_BETWEEN(SYSDATE,fisa.data_imprumut))||' luni.' luni
from fisa_de_lectura fisa, cititori c, carti ca,(select fisa.cititor_id, count(*)
                                        from fisa_de_lectura fisa
                                        group by fisa.cititor_id
                                        having count(*) = (select max(count(*))
                                                            from fisa_de_lectura
                                                            group by cititor_id)) aux
where c.cititor_id = aux.cititor_id
and fisa.cititor_id = c.cititor_id
and fisa.carte_id = ca.carte_id;



/* Afisati numele biblioteci si id-ul managerului biblioteci sau mesajul 'Nu are manager' daca biblioteca nu are manager*/


select b.denumire nume_biblioteca, nvl(to_char(b.manager_id), 'Nu are manager') as manager
from biblioteci b
order by 2 desc;

/* Afisati numele, mesajul 'Angajatul are salariul > 6000' daca angajatul are salariul > 6000 sau
mesajul 'Angajatul nu are salariul > 6000' in caz contrar si numarul zilelor ramase pana la 
sfarsitul luni relativ cu data angajari salariatului*/

with salariu6000 as (
                        select angajat_id
                        from angajati
                        where salariu > 6000)
select  a.nume, case when a.angajat_id in (select angajat_id from salariu6000) then 'Angajatul are salariul > 6000'
else 'Angajatul nu are salariul > 6000' end as informatii_salariu, 
to_char(last_day(a.data_angajare), 'DD') - to_char(a.data_angajare, 'DD') zile_pana_la_sf_luni
from angajati a;





/* Actualizari/suprimari */

/* Stergeti toti manageri bibliotecilor(adica setati null acolo unde nu este null). */

UPDATE BIBLIOTECI
SET manager_id = null
WHERE manager_id is not null;

rollback;

/* Stergerea datelor gresite din FISA_DE_LECTURA*/

INSERT INTO FISA_DE_LECTURA
(fisa_id, permis_id, cititor_id, biblioteca_id, carte_id, 
data_imprumut, data_restituire)
VALUES(11, 10, 1, 5, 1, '11/05/1980', '30/05/1980');

DELETE FROM FISA_DE_LECTURA f
WHERE f.carte_id not in (
select carte_id
from comenzi
where biblioteca_id = f.biblioteca_id)
OR NOT EXISTS(
select 1
from permis_de_intrare
where cititor_id = f.cititor_id
and biblioteca_id = f.biblioteca_id);

rollback;

/* Corectarea salariilor angajatilor in cazul in care acesta este gresit */

INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (6, 'Adam', 'Nicusor', '9882264773829',
'0772.002.778', 'AdamNicusor@gmail.com', 5000, '10/05/1980', 1, 1);


UPDATE ANGAJATI a
SET a.salariu = (
select salariu_minim
from functii
where functie_id = a.functie_id)
where a.salariu < (
select salariu_minim
from functii
where functie_id = a.functie_id);

rollback;

--OUTER JOIN
/* Sa se afiseze informati despre manageri bibliotecilor */

SELECT b.denumire, a.nume, a.prenume, adr.judet, adr.localitate, adr.strada, adr.cod_postal
from biblioteci b, angajati a, adresa_ang aang, adrese adr
where b.manager_id = a.angajat_id (+)
and a.angajat_id = aang.angajat_id (+)
and aang.adresa_id = adr.adresa_id (+);


--DIVISION
/* Sa se afiseze toti angajati care locuiesc in toate judetele care au cel putin un 'o' in ele*/


INSERT INTO ANGAJATI(angajat_id, nume, prenume, cnp, telefon, email,
salariu, data_angajare, functie_id, biblioteca_id)
VALUES (6, 'Dumitru', 'Carol', '9966664773829',
'0772.205.288', 'DumitruCarol@gmail.com', 2500, '10/05/1980', 2, 2);

INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(6, 2);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(6, 4);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(6, 6);
INSERT INTO ADRESA_ANG(angajat_id, adresa_id)
VALUES(6, 8);

rollback;

/* Codul de mai sus este adăugat cu scopul de a afișa un răspuns, 
deoarece in baza de date nu există un angajat care să satisfacă cererea */

select *
from angajati a
where not exists (
                select 1
                from adrese adr
                where lower(judet) like '%o%'
                and not exists (
                                select 1
                                from adresa_ang adrang
                                where adr.adresa_id = adrang.adresa_id
                                and a.angajat_id = adrang.angajat_id));





/* Sa se afiseze toti furnizori care lucreaza cu toate editurile */

INSERT INTO FURNIZORI(furnizor_id, denumire, telefon, email, adresa_id)
VALUES (6, 'Auxiliar', '0774.789.316', 'Auxiliar@gmail.com', 5);

INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(1, 6);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(2, 6);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(3, 6);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(4, 6);
INSERT INTO LUCREAZA(editura_id, furnizor_id)
VALUES(5, 6);

rollback;

/* Codul de mai sus este adăugat cu scopul de a afișa un răspuns, deoarece in 
baza de date nu există un furnizor care să satisfacă cererea */

select *
from furnizori a
where not exists (
                 select 1
                 from edituri b
                 where not exists (
                                    select 1
                                    from lucreaza c
                                    where b.editura_id = c.editura_id
                                    and a.furnizor_id = c.furnizor_id));
