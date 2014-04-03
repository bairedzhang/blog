#mongoose

### 
	var mongoose = require('mongoose');
	var schema = mongoose.Schema;
	db = mongoose.createConnection(port);
	db.on('open',function(){
		console.log('database connect successfully');
	})；
	
	example:
		var  PersonSchema = new schema({name:String});
		var  PersonModel = db.model('Person',PersonSchema);
		var  personEntity = new PersonModel({name:'bairedzhang');
		personEntity.save();
		PersonModel.find({name:/^baired/},function(err,persons){
			
		});