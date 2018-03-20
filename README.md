# imRetard

const Client = require('mariasql');

const table = {
    "request": "request",
    "employee": "employee",
    "active": "active"
};


let c = new Client({
    host: 'localhost',
    port: 3306,
    user: 'root',
    password: 'toor',
    db: 'randomcoffee'
});

//clarify query
function postEmployee(req, res) {
    let item = req.body;
    const query = 'use randomcoffee; Insert into employee values (:employee_id, :name, :surname, :email, :department, :location)';
    let params = {
        employee_id: item.employeeId,
        name: item.name,
        surname: item.surname,
        email: item.email,
        department: item.local_ou_id_sup,
        location: item.location
    };
    c.query(query, params, function (err) {
        if (err) {
            res.status(400).send('failed to post');
            throw err
        } else {
            res.status(200).send('posted');
        }
    });
    c.end();
};

/**
 * Inserts new request into DB
 * @param req
 * @param res
 */
function postRequest(req, res) {
    let item = req.body;
    const query = 'Insert into request values (:employeeId, :active, :timestamp)';
    let params = {
        employeeId: item.employeeId,
        active: 1,
        timestamp: new Date().valueOf(),
    };
    c.query(query, params, function (err) {
        if (err) {
            res.status(400).send('failed to post');
            throw err
        } else {
            res.status(200).send('posted');
        }
    });
    c.end();
};

function postInactiveRequest(req, res) {
    let item = req.body;
    const query = 'Insert into request values (:employeeId, :active, :timestamp)';
    let params = {
        employeeId: item.employeeId,
        active: 0,
        timestamp: new Date().valueOf(),
    };
    c.query(query, params, function (err) {
        if (err) {
            res.status(400).send('failed to post');
            throw err
        } else {
            res.status(200).send('posted');
        }
    });
    c.end();
};

/**
 * retrieves employee based on employeeId
 * returns
 * @param req
 * @param res
 */
function getEmployeeById(req, res) {
    let item = req.body;
    const query = 'Select * from request where employeeId = :employeeId';
    let params = {employeeId: item.employeeId};
    c.query(query, params, function (err, rows) {
        if (err) {
            res.status(400).send('failed to retrieve data');
            throw err;
        } else {
            res.status(200).send(rows);
        }
    })
    c.end();

};

//clarify query
/**
 * retrieves all active requests from DB
 * @param req
 * @param res
 */
function getActiveRequests(req, res) {
    const query = 'Select * from request where active = 1';
    c.query(query, function (err, rows) {
        if (err) {
            res.status(400).send('failed to retrieve data');
            throw err;
        } else {
            res.status(200).send(rows);
        }
    });
    c.end();

}

/**
 * Sets request as inactive by setting active = 0
 * @param req
 * @param res
 */
function setRequestInactive(req, res) {
    let item = req.body;
    const query = 'Update request set active = 0 where request_id = :request_id';
    let params = {
        request_id: item.requestId,
    };
    c.query(query, params, function (err, rows) {
        if (err) {
            res.status(400).send('failed to retrieve data');
            throw err;
        } else {
            res.status(200).send(rows);
        }
    })
    c.end();
};

function getRandomParticipants(req,) {
    let item = req;
    const query = 'SELECT r.requestId, b.employeeId, b.localOuIdSup, b.name, b.surname, b.email \n' +
        'FROM employee b, employee a natural join request r WHERE a.localOuIdSup <> b.localOuIdSup \n' +
        'and a.employeeId <> b.employeeId and a.employeeId = :employeeId and r.active = 1;';

    let params = {
        employeeId: item.employeeId,
        // employeeId: 1,
    };
    c.query(query, params, function (err, rows) {
        if (err) {
            throw err;
        } else {
            return rows;
        }
    });
    c.end();
}



module.exports = {
    postEmployee: postEmployee,
    postRequest: postRequest,
    postInactiveRequest: postInactiveRequest,
    getEmployeeById: getEmployeeById,
    getActiveRequests: getActiveRequests,
    setRequestInactive: setRequestInactive,
    getRandomParticipants: getRandomParticipants
};

