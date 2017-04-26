Documentation
=============

Projet : cpp_plazza
-------------------
Projet réalisé par le groupe **paulet_a, champi_b et ailler_h**

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

Cette classe contient des fonctionnalités pour tester la méthode de chiffrement.
- test : teste le format de donnée grace a une collection de méthodes
- testRaw : test une donnée non chiffrée
- testCaesar : teste une donnée chiffrée avec Caesar
- testXor : teste une donnée chiffrée avec xor

