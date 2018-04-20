# System.Object 
**Alle** klassen C# zijn afstammelingen van de ``System.Object`` klasse. Indien je een klasse schrijft zonder een expliciete parent dan zal deze steeds System.Object als rechtstreekse parent hebben. Ook afgeleide klassen stammen dus af van System.Object. Concreet wil dit zeggen dat alle klassen System.Object-klassen zijn en dus ook de bijhorende functionaliteit ervan hebben.
>Because every class descends from ``Object``, every object "is an" ``Object``.

Indien je de System namespace in je project gebrukkt door bovenaan ``using System;`` te schrijven dan moet je dus niet altijd ``System.Object`` schrijven maar mag je ook **``Object``** schrijven.

# Hoe ziet System.Object er uit?
Wanneer je een lege klasse maakt dan zal je zien dat instanties van deze klasse reeds 4 methoden ingebouwd hebben, dit zijn uiteraard de methoden die in de ``System.Object`` klasse staan gedefinieerd:

|Methode| Beschrijving|
|-------| ------------|
|``Equals()``| Gebruikt om te ontdekken of twee instanties gelijk zijn. |
|``GetHashCode()``| Geeft een unieke code (hash) terug van het object; nuttig om o.a. te sorteren.|
|``GetType()``| Geeft het type (of klasse) van het object terug.|
|``ToPrint()``| Geeft een string terug die het object voorstel.|

## GetType()
Stel dat je een klasse Student hebt gemaakt in je project. Je kan dan op een object van deze klasse de GetType() -methode aanroepen om te weten wat het type van dit object is:
```java
Student stud1= new Student();
Console.WriteLine(stud1.GetType());
```
Dit zal als uitvoer de namespace gevolgd door het type op het scherm geven. Als je project bijvoorbeeld "StudentManager" heet (en je namespace dus ook) dan zal er op het scherm verschijnen: ``StudentManager.Student``.

## ToString()
Deze is de nuttigste waar je al direct leuke dingen mee kan doen. 
Wanneer je schrijft:
```java
Console.WriteLine(stud1);
```
Wordt je code eigenlijk herschreven naar:
```java
Console.WriteLine(stud1.ToString());
```
Op het scherm verschijnt dan ``StudentManager.Student``. Waarom? Wel, de methode ToString() wordt in System.Object() ongeveer als volgt beschreven:
```java
public virtual string ToString()
 { return GetType(); }
 ```
 Merk twee zaken op:
 1. GetType wordt aangeroepen en die output krijg je terug.
 2. De methode is **virtual** gedefinieerd.
 **Alle 4 methoden in System.Object zijn ``virtual`` , en je kan deze dus ``override``'n!**
 
 ### ToString() override'n
 Het zou natuurlijk fijner zijn dat de ToString() van onze student nuttigere info teruggeeft, zoals bv de interne Naam (string autoprop) en Leeftijd (int autoprop). We kunnen dat eenvoudig krijgen door gewoon ToString to override'n:
 ```java
 class Student
 {
   public int Leeftijd {get;set;}
   public string Naam {get;set;}
   
   public override string ToString()
   {
       return $"Student genaamd {Naam} (Leeftijd:{Leeftijd})";
   }
 }
 ```
 Wanneer je nu ``Console.WriteLine(stud1);`` (gelet dat hij een Naam en Leeftijd heeft) zou schrijven dan wordt je output: ``Student Tim Dams (Leeftijd:35)``.
 
 
 ## Equals()
 Ook deze methode kan je dus override'n om twee objecten met mekaar te testen, bijvoorbeeld:
 ```java
if(stud1.Equals(stud2))
   //...
```
De euals methode heeft dus als signatuur: ``public virtual bool Equals(Object o)`` 
Twee objecten zijn gelijk voor .NET als aan volgende afspraken wordt voldaan:
* Het moet ``false`` teruggeven indien het argument o ``null`` is
* Het moet ``true`` teruggeven indien je het object met zichzelf vergelijkt (bv ``stud1.Equals(stud1)``)
* Het mag enkel ``true`` teruggeven als volgende statements beide waar zijn: 
```java
stud1.Equals(stud2);
stud2.Equals(stud1);
```
* Indien ``stud1.Equals(stud2)`` true teruggeeft en ``stud1.Equals(stud3)`` ook true is, dan moet ``stud2.Equals(stud3)`` ook true zijn.

### Equals overriden
Stel dat we vinden dat een student gelijk is aan een andere student indien z'n Naam en Leeftijd dezelfde is, we kunnen dan de Equals-methode override'n als volgt:
```java
//In de Student class
public override bool Equals(Object o)
{
     bool gelijk;
     if(GetType() != o.GetType()) 
         gelijk=false;
     else
     {
         Student temp= (Student)o; //Zie opmerking na code!
         if(Leeftijd== temp.Leeftijd && Naam== temp.Naam)
            gelijk=true;
         else gelijk=false;
      }
       return gelijk;
}
```

De lijn ``Student temp= (Student)o;`` zal het object o casten naar een Student. Doe je dit niet dan kan je niet aan de interne Student-variabelen van het object o. [Dit concept het polymorfisme en wordt later uitgelegd](/11_polymorfisme/README.MD).

## GetHashcode
Indien je Equals override dan moet je eigenlijk ook GetHashCode override'n, daar er wordt verondersteld dat twee gelijke objecten ook dezelfde unieke hashcode teruggeven. Wil je dit dus implementeren dan zal je dus een (bestaand) algoritme moeten schrijven dat een uniek nummer genereert voor ieder niet-gelijke object. Bekijk volgende [StackOverflow post](https://stackoverflow.com/questions/9827911/how-to-implement-override-of-gethashcode-with-logic-of-overriden-equals) indien je dit wenst toe te passen.