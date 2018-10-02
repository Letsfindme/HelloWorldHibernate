
# L'application que vous venez de créer contient ces fichiers:

La classe persistante 'Lecturer.java'.
Fichier de mappage 'Lecturer.hbm.xml'.
Le fichier de configuration 'hibernate.cfg.xml'.
La classe de test 'SimpleTest.java'.
Nous allons maintenant expliquer le code dans ces fichiers.

## La classe persistante 'Lecturer.java'.
Comme expliqué dans la section précédente, nous devons avoir une classe correspondant à chaque table d'entités dans notre base de données. Les instances de cette classe seront utilisées pour représenter les enregistrements de base de données. Par conséquent, la classe doit être capable de transporter des données pouvant exister dans de tels enregistrements, et puisque le maître de conférences dispose des colonnes:

ID (int),
FName (varchar (50)), et
LName (varchar (50)).
La classe correspondante doit avoir une variable de type 'int' et deux variables de type 'String'. Dans notre exemple, ils sont:
int ID
String firstName
String lastName 
Il est préférable de ne pas fournir un accès direct aux variables, mais d'utiliser des méthodes d'accès aux variables. C'est pourquoi les variables instantanées sont "privées" et il existe une méthode getter et une méthode setter pour chacune. Hibernate peut reconnaître les méthodes d'accès de la forme getVariablename et setVariablename.


_
## Le fichier de mappage 'Lecturer.hbm.xml':
La section précédente a montré que nous avons besoin d'un fichier de mappage pour indiquer à Hibernate, quelle classe représente quelle table et quelle variable instantanée correspond à quelle colonne.
Dans notre exemple, nous avons:
Une classe "Lecturer" correspondant à la Table "Lecturer", ceci est déclaré ainsi:
           <class name="Lecturer" table="Lecturer">
On peut lire ceci: La classe avec le nom «Lecturer» correspond à la table «Lecturer».
Dans cette classe, il y a les variables instantanées:
'prénom et nom'.
Ces variables correspondent aux colonnes 'FName' et 'LName'. Cette relation peut être déclarée de cette manière:
<property name="firstName" column="FName" type="string"/> 
<property name="lastName" column="LName" type="string"/>
La propriété avec le nom 'firstName' correspond à la colonne 'FName'.
Cette propriété est de type 'string'.
Il existe d'autres attributs utilisés avec l'élément 'propriété' pour guider Hibernate lors de la création des requêtes. Ces attributs sont expliqués ici.
ID
Cette variable, en plus de correspondre à l'ID de la colonne, représente la clé primaire de la table ... Et puisque le rôle d'un fichier de mappage est de guider Hibernate lors de la construction des requêtes; Hibernate doit savoir, à partir d’un fichier de mappage, comment la clé primaire est générée. Est-ce l'identité, donc la base de données va le générer? Ou l'application va le définir manuellement? Ou Hibernate devra le générer? ... etc.
Dans notre exemple, nous l'avons déclaré ainsi:
<id name="ID" type="int">
       <generator class="increment"/>
</id>                   

## La clé primaire de la table est représentée par la variable instantanée nommée 'ID'.
La classe de générateur de la clé primaire est «incrément». Cela signifie que Hibernate va le définir en incrémentant de 1 la valeur maximale de l'ID dans la table. En d'autres termes, Hibernate sélectionnera l'ID max dans le tableau; Ajoutez-y 1; définir la valeur ID du nouvel objet sur la valeur calculée.
Notez que l'attribut de colonne n'est pas défini. Cela est dû au fait que le nom de la variable est identique au nom de la colonne et que la valeur par défaut d'Hibernate pour l'attribut column est la valeur de l'attribut name.

Création d'un fichier XML pour les configurations Hibernate 'hibernate.cfg.xml'
La section précédente indiquait que la mise en veille prolongée devait connaître certaines informations sur la base de données et comment gérer les connexions avec celle-ci, et que ces informations pouvaient être fournies.
Programatically, or
in XML file, or
in .properties file
 Dans notre exemple, nous avons utilisé un fichier XML pour définir les configurations Hibernate.

## Le fichier 'hibernate.cfg.xml' contenait les configurations de base nécessaires pour obtenir et gérer les connexions; ces configurations sont:

L'URL de la source de données
                   <property name="connection.url">jdbc_URL</property>            

Nom d'utilisateur et mot de passe SQL Server
                <property name="connection.username">jdbc_Username</property>
              <property name="connection.password">jdbc_Password</property>            

## La classe de notre Driver
<property name="connection.driver_class">
                        sun.jdbc.odbc.JdbcOdbcDriver
</property>            
Le dialecte : Il informe Hibernate de la base de données sur laquelle il va générer ses requêtes. (c’est-à-dire que la syntaxe des requêtes générées pour une base de données SQL Server est différente de la syntaxe des requêtes générées pour une base de données Oracle). Et comme nous utilisons SQL Server, nous utilisons le dialecte «dialect.SQLServerDialect» et le représentons de la manière suivante:
  <property name="dialect">
                        org.hibernate.dialect.SQLServerDialect
</property>         

Current_session_context_class, qui indique à Hibernate comment lier la session.
<property name="current_session_context_class">thread</property>            
La valeur 'Thread' signifie que la session sera liée "au thread à partir duquel openSession (...) est appelé" 

La classe de test 'SimpleTest.java'.
Enfin, après toutes ces préparations, nous pouvons maintenant utiliser Hibernate.

Comme indiqué précédemment, Hibernate entretient des conversations avec la base de données. Ces conversations sont présentées par la «session». Nous obtenons cette session à partir de la "SessionFactory" en utilisant la méthode “getCurrentSession ()” ou “ouverture de session”. Pour la SessionFactory, afin de pouvoir gérer les sessions, il convient de connaître les "Configurations" que nous venons de définir. Nous créons donc un objet de type "Configuration" qui utilise 'hibernate.cfg.xml'.

Nous devons donc,

Créez l'objet 'Configuration'.
Configuration configuration = new Configuration();
Demandez-lui de compiler nos configurations prédéfinies.
configuration.configure();
Cela aurait pu être fait des trois manières mentionnées précédemment.
De manière problématique, de cette façon
configuration.setProperty ("hibernate.connection.url", "jdbc_URL"); 
Dans un fichier .properties. Par défaut, un nouvel objet Configuration utilise les propriétés définies dans 'hibernate.properties'
Dans un XML en appelant la méthode .configure Par défaut, la méthode .configure () utilise les éléments de 'hibernate.cfg.xml'. Si vous voulez nommer un autre nom pour votre fichier de configuration, vous devez spécifier ce fichier en appelant l’une des surcharges de la méthode, comme par exemple:
configuration.configure("conf.xml");
Créez une SessionFactory avec ces configurations.
SessionFactory sessionFactory = configuration.buildSessionFactory();
Commencer une conversation DB en obtenant une session
Session session = sessionFactory.getCurrentSession();
Pour moins de code, nous avons écrit les trois premières déclarations de cette façon.
SessionFactory sessionFactory = new
                        Configuration().configure().buildSessionFactory();          
Maintenant, nous allons inclure nos commandes dans une transaction
Transaction tx = session.beginTransaction();
            
            tx.commit();          
Puisque nous voulions insérer un nouvel enregistrement dans la table du lecturer, et qu'Hibernate nous permet d'oublier les enregistrements, tout ce dont nous avons besoin est de sauvegarder (c'est-à-dire créer et enregistrer) un nouvel objet de lecturer.

  Lecturer lecturer1 = new Lecturer();
          lecturer1.setFirstName("Fat");
          lecturer1.setLastName("Mat");
          session.save(lecturer1);        
          
Et c'est le code que vous avez collé dans SimpleTest.java
Exécutez cette requête:
                insert into Lecturer (FName, LName, ID) values ('Fatma','Meawad', '8')              
Pour pouvoir voir les requêtes exécutées par Hibernate, ajoutez cette instruction à 'hibernate.cfg.xml'


            <property name="hibernate.show_sql">true</property>

Comme nous en avons fini avec notre exemple simple, nous devrions passer à un exemple plus avancé, mais vous devez d'abord connaître les différents «états des objets» dans une application Hibernate.
Tout au long de la partie précédente, nous avons utilisé les mots "Objets persistants" et "Persistants". 
Alors, quels sont les objets persistants et quels sont les autres types d'objets?
Définissons d'abord 'Persistance' ... Selon Wikipédia, Persistance "fait référence aux caractéristiques des données qui survivent à l'exécution du programme qui les a créées".
Ainsi, par exemple, si vous utilisez un logiciel de calcul traditionnel, les données utilisées dans l’application ne sont pas persistantes, car une fois l’application fermée, les données ne sont plus présentes (c.-à-d. qui l'a créé "). Mais si votre logiciel enregistre les calculs dans une base de données par exemple, ces données sont persistantes (il «survit à l’exécution du programme qui l’a créé»)
Ainsi, les objets persistants sont les objets dont les données ne sont pas perdues lors de la fermeture de l'application (il existe des enregistrements de base de données contenant les valeurs exactes des variables des objets).
Retour aux états des objets Hibernate; Ce sont trois états:
État persistant.
Il est garanti que l'objet représente un enregistrement de base de données. Ce sont des objets attachés à une session Hibernate. Ce sont des objets enregistrés, rentrés ... etc par Hibernate.
Dans notre exemple, le lecturer objet est persistant après cette
session.save(lecturer1)
État transitoire
Ce sont des objets normaux, utilisés tout au long de l'application, et perdent leurs valeurs une fois l'application fermée.
Dans notre exemple, le lecturer d'objet est transitoire avant cette session  session.save(lecturer1)
Objets détachés.
Ce sont des objets qui une fois persistants (attachés à un certain contexte), mais alors ce contexte est fermé. Il n'est pas garanti que cet objet reflète l'état de la base de données.

Dans notre exemple, le conférencier objet est détaché après la mise en état
tx.commit()
Testez-le :
Imprimer l'identifiant de l'objet lecturer1
Avant session.save (lecturer1);
Après session.save (lecturer1);
Ajouter lecturer1.setLastName ("TestName");
Entre session.save (lecturer1); 
et tx.commit ().
Après tx.commit (); 
