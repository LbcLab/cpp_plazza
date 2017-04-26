Documentation
=============

Projet : cpp_plazza
-------------------
Projet réalisé par le groupe **Charles Paulet, Theophile Champion et Hugo Ailleres**

Sommaire :
----------

*	[_Core_](#Core)
*	[_IDBManager_]()
*	[_IGraph_]()
*	[_IProcessManager_]()
	*	[_Process_]()
	*	[_ThreadPool_](#ThreadPool)
	*	[_Timer_](#Timer)
*	[_Ciphers_](#Ciphers)
	*	[_Cipher_](#Cipher)
	*	[_CaesarCipher_](#CaesarCipher)
	*	[_XorCipher_](#XorCipher)

Core <a id = "Core"></a>
------------------------

Cet objet est le _coeur_ même du projet

```
#pragma once
#ifndef CORE_HH_
# define CORE_HH_
class Core
{
public:
  Core(size_t maxThreads);
  ~Core();
  void run();
private:
  DBManager       *db;
  IGraph          *graph;
  ProcessManager  *processManager;
};
#endif
```
#### Fonctionnalitées

* _run()_ est la fonction appelée par le _main()_
* Les attributs [_db_](), [_graph_]() et [_processManager_]() sont expliqués dans la suite de la documentation

### ThreadPool <a id = "ThreadPool"></a>
```
#pragma once
#ifndef THREADPOOL_HH_
# define THREADPOOL_HH_
typedef std::chrono::time_point<std::chrono::system_clock> Time;
class ThreadPool
{
public:
  ThreadPool(size_t maxThreads);
  ~ThreadPool();
  bool createThread(void (*func)(void *param), ThreadPool *threadPool);
  int  joinAll();
  const Time &getTimeOfLastJob() const;
  std::string popTodoTasks();
  void pushTodoTasks(const std::string &task);
  std::string popDoneTasks();
  void pushDoneTasks(const std::string &task);
private:
  Time timeOfLastJob;
  /*
  ** Taks.
  */
  std::queue<std::string> todoTasks;
  std::queue<std::string> doneTasks;
  /*
  ** Thread and Mutex.
  */
  std::mutex mutex;
  size_t maxThreads;
  std::vector<std::thread> threads;
};
#endif
```

#### Fonctionnalitées :

*	_createThread(void (\*func)(void \*param), void \*param)_ créée un nouveau fil d'éxécution à partir de celui donné en paramètre, et lui affilie une tâche
* _joinAll()_ supprime tous les fils d'éxécutions de la ThreadPool
* _getTimeOfLastJob()_ retourne l'heure de la dernière action éxécuté par la ThreadPool (cela permet de quitter au bout d'un temps _t_)
*	 _popTodoTasks()_ retourne la prochaîne tâche à accomplir, et la supprime de sa _queu_
*	_popDoneTasks()_ retourne la dernière tâche accomplie, et la supprime de sa _queu_
*	_pushTodoTasks(const std::string &task)_ ajoute une tâche à accomplir dans la _queu_
*	_pushDoneTasks(const std::string &task)_ ajoute une tâche à accoplir dans la _queu_
*	_todoTasks_ représente la _queu_ des tâches à accomplir
*	_doneTasks_ représente la _queu_ des tâches accomplies
*	_mutex_ protège une ressource à l'intérieur d'un processus
*	_maxThreads_ correspond au nombre maximum de fils d'éxécutions par processeur
*	_threads_ contient les différents fils d'éxécutions de la _ThreadPool_

### Timer <a id = "Timer"></a>

```
#pragma once
#ifndef CORE_HH_
# define CORE_HH_
class Timer
{
public:
  Timer();
  ~Timer();
  void start(int sec);
  int  getRemainingTime();
private:
  std::chrono::time_point<std::chrono::system_clock> begin;
  int waitingTime;
};
#endif
```

#### Fonctionnalitées :

* _start(int sec)_ lance le minuteur
* _getRemainingTime()_ retourne un entier correspondant au temps restant par rapport à l'appel de _start(int sec)_
* _begin_ contient l'heure du dernier appel de _start(int sec)_
* _waitingTime_ correspond au dernier paramètre donné à _start(int sec)_



Ciphers <a id = "Ciphers"></a>
------------------------

Les ciphers font parti d'un direcroty spécifique dans src/cipher/. Cette partie du code est chargé de s'occuper de chiffrer, 
déchiffrer, reconnaître ainsi que de procéder à différentes analyses sur de la donnée chiffée ou non.
On y retrouve les classe Cipher, CaesarCipher et XorCipher

### Cipher <a id = "Cipher"></a>

Cipher contient un point d'entrée par lequel on donne le nom du fichier ainsi
que le type d'information recherché <br />
Ce point d'entrée est :<br />

void findData(std::unique\_ptr<IFile> &file, plz::Information);

Le point d'entrée du déchiffrement est la fonction membre decipher();<br />
Cette fonction accepte une partie du fichier afin de ne pas procéder à des tests
sur l'ensemble des fichiers et un plz::Information afin de rechercher et de valider
une information.

Une partie de ce fichier va être utilisée pour détecter le type de chiffrement.

Cette classe contient des fonctionnalités pour tester la méthode de chiffrement.
- bool test(char \*const)  : teste le format de donnée grace a une collection de méthodes
- bool testRaw(char \*const, plz::Information) : test une donnée non chiffrée pour l'information demandée
- bool testCaesar(char \*const, plz::Information) : teste une donnée chiffrée avec Caesar pour l'information demandée
- bool testXor (char \*const, plz::Information) : teste une donnée chiffrée avec xor pour l'information demandée

La fonction membre test() essaye de déterminer le type de fichier. Après dénombrement
des fichiers donnés avec le sujet, nous avons établi la liste de priorité suivante :
- 676 gif
- 278 zip
- 263 html
- 131 jpg
- 69 txt
- 42 PI1
- 33 LST
- 17 php3
- 12 png
- 3 css
- 3 jar
- 3 mdz
- 3 xmz
- 2 ym
- 2 lst
- 2 class
- 2 prg
- 2 pi1
- 1 rtf
- 1 S
- 1 TXT
- 1 swf
- 1 MOD
- 1 MUS
- 1 diz
- 1 SND
- 1 msa

Le numéro de gauche dénombre les fichiers définis par le litéral à leur droite.
Le Cipher contient également divers outils permettant de réaliser des analyses
sur le fichier. Leur existence est historique puisque les tests sur les fichiers
donnés ne se sont pas révélés assez explicites.<br />

- std::size\_t getDistributionSignature(std::map<char, double> map) const;
- std::map<char, double> englishDistribution(void) const;
- std::map<char, double> frequencyAnalysis(std::string const &str);

Une fois les tests réalisés, le fichier est déchiffré et une fonction membre <br />
const std::vector<std::string> &getCollectedData() const;<br />
est appelée pour récupérer les données trouvées dans le fichier.

Des tests unitaires sont présents dans src/cipher/main.cpp

CaesarCiphers <a id = "CaesarCipher"></a>
------------------------

Cette classe est instanciée par Cipher, elle est détenue sous la forme d'un unique ptr<br />
Elle contient deux fonctions membre destinées à chiffrer/déchiffrer et à bruteforcer un code.

- std::string caesar_cipher(std::string const &str, std::size_t shift);
- std::shared_ptr<std::vector<std::string> > bruteforce_caesar(std::string const &);

Des tests unitaires sont présents dans src/cipher/main.cpp

XorCiphers <a id = "XorCipher"></a>
------------------------

Cette classe contient deux fonctions membres pour chiffrer/déchiffrer grâce à une
clef en char const [2] ou en std::string const.<br />
Une troisième méthode est là pour bruteforcer le code. Elle renvoie les valeurs
possibles sous forme d'un pointeur partagé sur un vecteur de string.<br />

- std::string xor_cipher(std::string const, char const [2]);
- std::string xor_cipher(std::string const, std::string const);
- std::shared_ptr<std::vector<std::string> > bruteforce_xor(std::string const);

Des tests unitaires sont présents dans src/cipher/main.cpp
