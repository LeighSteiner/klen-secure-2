


klen-secure is an npm module to create flexible, customizeable backend route security. It is dependent on passport (or koa-passport), in order to make use of the request user object after log in, and can be used with either express or koa configuration.  The default setting is express, and the default auth tests (primarily for example use), require Sequelize.

Your authorization functions are kept as private properties scoped so that the main functions of the module can use them, but they cannot be overwritten or manipulated elsewhere in your application.

The authFailLog function takes a parameter "whichAuth" to specify which clearance level should be checked, and should be passed as middleware into any route (see Usage examples below) which requires a particular clearance level to access. authFailLogger handles both preventing and allowing access appropriately, based on a users clearances, and creates a log of userIds which make any particular bad request, allowing you to see which users are attempting to access protected areas of your application.  The first time any user encounters a secured route, they will have their clearances checked and attached to the request, and not require re-checking any time again during that session.  Finally, getAuthFailLog (which can itself be set behind a secured piece of middleware), will reveal the log of bad requests, including the attempted route, user id, timestamp and ip address associated with each request. 

The 'modelAuthenticator' parameter allows you to use the default Sequelize authFuncs with a model that is not called 'user' (eg, player). 


Usage:

Instantiate - 
`const klenSecure = require('./routes/klen-secure-test.js')();`
`const klen = new klenSecure(modelAuthenticator, authObject, logViewBool, config)`

AuthMiddleware - 

The function authFailLogger both assigns a user's clearances if they're not already attached to the user object, and checks the clearance level of the user against the clearance specified in the route.  If the user is not allowed to clear the route, their userId, ipAddress and date/time will be added to the fail log.  

const defineMiddleware = klen.authFailLogger(whichAuth); 
app.use(defineMiddleware)

app.get('/fail-log', klen.getAuthFailLog());


default AuthFunctions:
	isUser : async (id) => {                       
		let user = await this.modelAuthenticator.findById(id)
			return !!user;
	}, 
	isMod : async (id) => {
		let user = await this.modelAuthenticator.findById(id)
		return !!user.isMod;
	},
	isAdmin: async (id) =>{
		let user = await this.modelAuthenticator.findById(id)
		return !!user.isAdmin; 
	},
	isSiteController : async (id) => {
		let user = await this.modelAuthenticator.findById(id)
		return !!user.isSiteController;
	}




