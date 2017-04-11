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

### Timer

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
