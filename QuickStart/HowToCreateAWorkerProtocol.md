# How to create a worker subprotocol

1. Create a folder under `[yourNoXerveRepoPath]/src/node/worker/worker_object_manager/`. Folder name is worker_object_name. Then creat a Js script `index.js` inside.
2. Register module after `[yourNoXerveRepoPath]/src/node/worker/index.js` line 76 `Worker.prototype.start()`
3. Add a `createXXXX` function to worker. Substitude XXXX with your object name.
e.g.
```javascript
Worker.prototype.createWorkerGroup = function(worker_group_purpose_name, worker_peers_worker_id_list, callback) {
  this._worker_object_managers.worker_group.create(worker_group_purpose_name, worker_peers_worker_id_list, callback);
}
```
4. Implement a `WorkerXXXXManager` object in`index.js` in your `[yourNoXerveRepoPath]/src/node/worker/worker_object_manager/[worker_object_name]` folder. Substitude XXXX with your object name. You can take `index.js` in `worker_group` folder or other worker objects as reference. The manager should at least contains `create()`. At the end of `index.js`, `module.exports` should have `register_code` and `module`. About the code number, please refer to the document or count by yourself :face_palm:
5. (Optional) Create a test case in `/src/noxerve_agent/nodejs/tester/modules_tester.js`, e.g. `Worker.createWorkerShoaling()`. You can append the test case after other worker objects.
6. Create a folder under `[yourNoXerveRepoPath]/src/noxerve_agent/nodejs/protocol/protocols/worker/worker_subprotocols`. The same, folder name is worker_object_name. Then create `index.js`, `manager.js` and `[worker_object_name].js` inside.

A quick clear up here. `manager.js` is the scipt manager of `[worker_object_name].js` object, controling life cycle related stuff like `create()`. The manager will be scanned by the subprotocol manager and added to a dictionary, then passed to `index.js` under`worker/worker_object_manager/[worker_object]` as  `_worker_subprotocol_object_managers`. A worker object is created using the `create()` in the corresponding manager.

7. `manager.js` should contains `create()` and event system(event dictionary, `on()` and `emit()`). `worker-XXXX-create-request` event would be registered in `start()` in `index.js` already, so when `create()` is called just trigger the event with params passed.

8. `index.js` is the core part of subprotocol. Should at least contains `start(callback)`. Basically, `start()` registers the `worker-XXXX-create-request` event, which will create a worker XXXX object and pass it back. Also the manager should be passed back with callback. At the end, shoud have `module.exports = {}` contains `protocol_name`m `protocol_code` and `module`.
