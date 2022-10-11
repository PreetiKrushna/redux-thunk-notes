# Filse Structure

$
1. Action
2. Reducer
3. Constant Type
4. Store
$

# Dependency packages for Redux thunk
$ 
1. npm install redux
2. npm install react-redux
3. npm install redux-thunk
4. npm i redux-saga
5. npm install axios
6. npm install redux-devtools-extenstion
$


# Import Dependency
$ 
1. import {createStore, applyMiddleware, combineReducers} from 'redux';
2. import thunk from 'redux-thunk';
3. import {composeWithDevTools} from 'redux-devtools-extension';
4. import logger from 'redux-logger';
5. import {Provider} from 'react-redux';
6. import {useSelector, useDispatch} from 'react-redux';
$



## Example

# Parent App
--------------
$
import React from 'react';
import {Provider} from 'react-redux';
import Home from './components/Home'
import store from './store';

const PracticeRoute = () => {
  return (
    <Provider store={store}>
      <Home/>
    </Provider>
  )
}

export default PracticeRoute
$

# Store
----------
$
import {createStore, applyMiddleware, combineReducers} from 'redux';
import thunk from 'redux-thunk';
import {composeWithDevTools} from 'redux-devtools-extension';
import logger from 'redux-logger';
import {userReducer} from './redux/userReducer';


const initialState={};
const middleware= [logger, thunk]

const rootReducer = combineReducers({
   userList: userReducer
})

const store = createStore(
  rootReducer, 
  initialState, 
  composeWithDevTools(applyMiddleware(...middleware)));
export default store;
$

# Constant or Type
------------------
$
export const GET_USER_REQUEST='GET_USER_REQUEST'
export const GET_USER_SUCCESS='GET_USER_SUCCESS'
export const GET_USER_FAIL='GET_USER_FAIL'
$

# Reducer File
--------------
$
import {GET_USER_REQUEST, GET_USER_SUCCESS, GET_USER_FAIL} from '../redux/userType';



export const userReducer =(state={users:[]}, action)=>{
  switch(action.type){
    case GET_USER_REQUEST:
      return{
        loading: true,
        users:[]
      }
    case GET_USER_SUCCESS:
      return{
        loading: false,
        users: action.payload
      }
    case GET_USER_FAIL:
      return{
        loading: false,
        error: action.payload
      }
    default :
      return state;
  }
}
$


# Action 
-----------

$
import {GET_USER_REQUEST, GET_USER_SUCCESS, GET_USER_FAIL} from '../redux/userType';
import axios from 'axios';



export const userAction=()=> async(dispatch)=>{
  try{
    dispatch({
      type: GET_USER_REQUEST
    })
    const { data } = await axios.get('https://jsonplaceholder.typicode.com/comments');
    dispatch({
      type: GET_USER_SUCCESS,
      payload: data
    })
  } catch(error){
    dispatch({
      type: GET_USER_FAIL,
      payload: error.data && error.response.data.message ? error.response.data.message : error.message
    })
  }
}
$


# Home Component to Render


$
import React, {useEffect} from 'react';
import {useSelector, useDispatch} from 'react-redux';
import {userAction} from '../redux/userAction'
import UserList from './UserList';



const Home = () => {

  const dispatch = useDispatch();
  const userList = useSelector(state=>state.userList)
  const {loading, error, users} = userList;

  useEffect(()=>{
    dispatch(userAction())
  }, [dispatch])



  return (
    <div>
      {
        loading ? <h1>Loading......</h1>
        : error ? <h3>{error}</h3> 
        : <UserList users={users}/>
      }
    </div>
  )
}

export default Home
$

# Rendering Component or Uer Component

$
import React from 'react'

const UserList = ({users}) => {
  return (
    <div>
      {
        users.map((info)=>{
          return(
            <div key={info.id}>
              <h2>{info.email}</h2>
            </div>
          )
        })
      }
    </div>
  )
}

export default UserList
$