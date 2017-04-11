Documentation
=============

Sommaire :
----------

*	[_Core_](#Core)
*	[_IDBManager_]()
*	[_IGraph_]()
*	[_IProcessManager_]()

Core <a id = "Core"></a>
------------------------
```
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
```
