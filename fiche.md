Fiche de révision - Gestion des anomalies
=========================================

Lexique
-------

<p>
    Signalement synchrone : le client détermine l'anomalie.<br/>
    Signalement asynchrone : le client ne détermine pas l'anomalie.<br/>
    <br/>
    Traitement local : Gestion de l'erreur à la charge du fournisseur.<br/>
    Traitement global : Gestion de l'erreur à la charge du client.
</p>

Le nouvel essai - (Asynchrone / Local)
--------------------------------------

<p>
    Tant qu'il y a un échec on rappelle le fournisseur (erreur ou valeur incorrecte).
</p>

###Exemple :
<pre>
    <code>
boolean verif = true ;
do{
    try{
        System.out.println("Nombre entre 1 et 10");
        sc.nextInt() ;
        verif = !verif ;
    }
    catch (Exception e){
        System.out.println("Erreur") ;
    }
}while(verif) ;
    </code>
</pre>

Le contrat - (Synchrone / Global)
---------------------------------

<p>
    Définit la confiance mutuelle entre le client et le fournisseur.<br/>
    Avec une précondition dans le client et une postcondition dans le fournisseur (sous forme d'assert).<br/>
    Attention : En java, il faut activer l'option enable assertion dans la machine virtuelle java, l'option -ea
</p>

###Exemple :
<pre>
    <code>
//Client
//[...]
System.out.println("Charge du produit :");
charge = sc.nextInt();
if (conteneur.getCharge() + charge <= Conteneur.CHARGE_UTILE) {  //précondition
    produit = new Produit(charge);
    conteneur.ajouter(produit);
}
else
    estPlein = true;
nb++;
//[...]


//Fournisseur
public void ajouter(Produit produit) {
    //postcondition
    assert this.charge + produit.getCharge() <= Conteneur.CHARGE_UTILE :
            "ajout impossible !";
    this.charge = this.charge + produit.getCharge();
    this.contenu.add(produit);
}
    </code>
</pre>

La propagation - (Asynchrone / Global)
--------------------------------------

<p>
    Indique au client qu'un problème a eu lieu et qu'il doit le traiter, par le biais d'Exception.<br/>
    Attention, si besoin, créer une classe qui hérite d'Exception personnalisée.
</p>

###Exemple
<pre>
    <code>
//Nouvelle Exception
public class ErreurLigne extends Exception {
    
    public ErreurLigne() {
        super("Error ligne");
    }
}

//Fournisseur
public int lireNumLigne(int num) throws ErreurLigne {
    if (num > 10){
        return num ;
    }else{
        throw new ErreurLigne() ;
    }
}

//Client
//Si la fonction n'est pas un main, ne pas oublier de 
//propager l'erreur avec un throws dans l'entête
public static void main (String[] args){
    try{
        ligne = lireNumLigne(num) ;
    }
    catch(ErreurLigne e){
        System.out.println("Problème sur la ligne") ;
    }
}
    </code>
</pre>

Le code de retour - (Synchrone / Global)
----------------------------------------

<p>
    Le fournisseur retourne un indicateur d'échec ou de réussite.<br/>
    Exemple : <br/>
    Si plusieurs erreurs : 0 lorsque tout va bien, 1..n où chaque chiffre correspond à une erreur différente. <br/>
    Sinon : True or False
</p>

###Exemple
<pre>
    <code>
//Fournisseur
public boolean action(String jour){
    if(jour.equals("lundi"))
        return true ;
    else
        return false ;
}

//Client
public static void main (String[] args){
    String jour = "lundi" ;
    if(action(jour))
        System.out.println("OK !") ;
    else
        System.out.println("Non !") ;
}
    </code>
</pre>

Le déroutement - (Asynchrone / Local)
-------------------------------------

<p>
    Le fournisseur appel une procédure chargée de gérer l'erreur.
</p>

###Exemple
<pre>
    <code>
public int add(int day){
    if (day+1 > 7)
        return monday() ;
    else
        return day+1 ;
}

public int monday(){
    //1 représente lundi
    return 1 ;
}
    </code>
</pre>

Le test de validité - (Synchrone / Global)
------------------------------------------

<p>
    On utilise un attribut de classe permettant d'indiquer la présence d'erreurs.<br/>
    L'attribut est mis à jour par le fournisseur et est disponible pour le client à tout moment.<br/>
    Le test et la gestion de l'erreur sont gérés par le client.<br/>
    <br/>
    En général, le test de validité se fait à la toute fin du programme.
</p>

###Exemple :

<pre>
    <code>
//Fournisseur
public class Week {
    private boolean validity ;
    
    public Week(){
        validity = true ;
    }
    
    public boolean getValidity(){
        return this.validity ;
    }
    
    public void add(int day){
        if (day+1 > 7)
            this.validity = false ;
        else
            return day+1 ;
    }
    
    public int monday(){
        //1 représente lundi
        this.validity = true ;
        return 1 ;
    }
}

//Client
public static void main (String[] args){
    Week week = new Week() ;
    int day = 7 ;
    week.add() ;
    if (!week.getValidity)
        week.monday() ;
}
    </code>
</pre>