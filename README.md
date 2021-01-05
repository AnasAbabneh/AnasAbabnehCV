# Repository Design Patterns
-RepositoryDataAccess => [Entity , Context , Migration , DTO]
 public interface IGeneric<T> where T : class
    {
        void insart(T obj);
        void Update(T obj);
        void Delete(int id);
        T Find(int id);
        List<T> LoadAll();

    }
 public class Generic<T> : IGeneric<T> where T : class
    {
        readonly Context.Context db = new Context.Context();

        public void Delete(int id)
        {
            T T_ID = db.Set<T>().Find(id);
            db.Set<T>().Remove(T_ID);
            db.SaveChanges();
        }

        public T Find(int id) => db.Set<T>().Find(id);

        public void insart(T obj)
        {
            db.Set<T>().Add(obj);
            db.SaveChanges();
        }

        public List<T> LoadAll()
        {
            return db.Set<T>().ToList();
        }

        public void Update(T obj)
        {
            db.Set<T>().Attach(obj);
            db.Entry(obj).State = EntityState.Modified;
            db.SaveChanges();
        }
    }
------------------------------------------
-RepositoryyBusinessLogic [ RepositoryClass, RepositoryInterface ]

public interface IXRepository
    {
        void Insert(X obj);
        void Update(X obj);
        void Delete(int ID);
        X Load(int ID);
        List<X> LoadAll();
    }
    
     public class XRepository : IXRepository
    {
        void IXRepository.Insert(X obj)
        {
            IGeneric<X> x = new Generic<X>();
            x.Insert(obj);
        }


        void IXRepository.Update(AttributeValue obj)
        {
            IGeneric<X> x = new Generic<X>();
            x.Update(obj);
        }


        void IXRepository.Delete(int ID)
        {
            IGeneric<X> x = new Generic<X>();
            x.Delete(ID);
        }


        X IXRepository.Load(int ID)
        {
            IGeneric<X> x = new Generic<X>();
            x attrvaluedObj.Load(ID);
        }

        List<X> IXRepository.LoadAll()
        {
            IGeneric<X> x = new Generic<X>();
            x attrvaluedObj.LoadAll();
        }


    }

+  List<VMX> IXRepository.LoadVMx()
        {
            XXContext context = new XXContext();
             var x = context.XEntity.GroupJoin(
                         context.X1Entity,
                         A => A.ID,
                         V => V.Attributes_ID,
                        (X, Y) => new { X.ID, X.Name , X.Status , count= Y.Count() });
 
 List<VMX> li = new List<VMX>();
           
            foreach (var item in x)
            {
                VMX obj = new VMX();
                obj.listattr = new aAttributes();
                obj.listattr.ID = item.ID;
                obj.listattr.Name = item.Name;
                obj.listattr.Status = item.Status;
                obj.count = item.count;
                li.Add(obj);

            }

            return li;
     }
+ public User MainAdmin()
        {
            MessagingSystemContext db = new MessagingSystemContext();

            var AdminFirest = (from user in db.UserEntity
                         where user.Type == "Admin"
                         select user).FirstOrDefault();
            return AdminFirest;
        }
  -----------------------------------
  
  -WebApi [ controller ]
  public class UserController : ApiController
    {

        //Post User
        public void Post(User obj)
        {
            IUserRepository userObj = new UserRepository();
            userObj.Insert(obj);
        }

        //Get List User
        public List<User> Get()
        {
            IUserRepository userObj = new UserRepository();
            return userObj.LoadAll();
        }

        //Get User
        [Route("api/User/find")]
        [HttpGet]
        public User Getfind(int id)
        {
            IUserRepository userObj = new UserRepository();
            return userObj.Load(id);
        }

        [Route("api/User/MainAdmin")]
        //Get MainAdmin
        [HttpGet]
        public User GetMainAdmin()
        {
            IUserRepository userObj = new UserRepository();
            return userObj.MainAdmin();
        }

        //Post Delete User
        public void Post(int id)
        {
            IUserRepository userObj = new UserRepository();
            userObj.Delete(id);
        }

        [Route("User/Update")]
        //Post Update User
        [HttpPost]
        public void Update(User obj1)
        {
            IUserRepository userObj = new UserRepository();
            userObj.Update(obj1);
        }
    }


+	Install-Package Microsoft.AspNet.WebApi.Cors
using System.Web.Http;
using System.Web.Http.Cors;
+ EnableCorsAttribute cors = new EnableCorsAttribute("http://localhost:4200", "*", "*");
            config.EnableCors(cors);
------------------------------------------------

MVC [ ajax , html , jq , js] 

  //////save data
                    $("#SaveChange").click(function () {
                        //debugger;
                        var nameprand = $('#txtprandname').val();
                        var states = $('#txtselect').val();

                        var dataString = {
                            'Name': nameprand,
                            'Status': states

                        };

                        $.ajax({
                            type: 'Post',
                            url: '/Api/api/Brand',
                            data: JSON.stringify(dataString),
                            contentType: 'application/json;charset=utf-8',
                            dataType: 'json',
                            success: function () {

                                // show add success
                                $("#showmsg").show();

                                // hide popup
                                $("#ModalPopUp").hide();

                                // delete table tr
                                $('#tableItems').each(function () {
                                    $("#tableItems tr").empty();

                                });


                                // add tr title
                                $("#tableItems").append(
                                    "<tr><td><a class='glyphicon glyphicon-retweet' style='margin-right:5%'> </a> Brand Name </td><td> <a class='glyphicon glyphicon-retweet' style='margin-right:5%'> </a> Status </td> <td><a class='glyphicon glyphicon-retweet' style='margin-right:5%'> </a> Action </td></tr>");


                                ///////Get 'reade'

                                $.ajax({
                                    type: 'Get',
                                    url: '/Api/api/Brand',
                                    dataType: 'json',
                                    success: function (data) {
                                        debugger;
                                        $.each(data, function (key, value) {
                                            if (value.Status == "Active") {
                                                $("#tableItems").append(
                                                    "<tr>  <td>" + value.Name + "</td > <td> <button type='button' class='btn btn-success  btn-xs'>" + value.Status + "</button></td> <td>  <button type='button' value='edit' class='glyphicon glyphicon-pencil' onClick=Edititem(\'" + value.ID + "\');></button>  <button type='button' class='glyphicon glyphicon-trash' value='Delete' onClick=deleteitem(\'" + value.ID + "\');></button>  </td>  </tr>");

                                            }
                                            else {
                                                $("#tableItems").append(
                                                    "<tr>  <td>" + value.Name + "</td > <td> <button type='button' class='btn btn-warning  btn-xs'>" + value.Status + "</button></td> <td>  <button type='button' value='edit' class='glyphicon glyphicon-pencil' onClick=Edititem(\'" + value.ID + "\');></button>  <button type='button' class='glyphicon glyphicon-trash' value='Delete' onClick=deleteitem(\'" + value.ID + "\');></button>  </td>  </tr>");

                                            }

                                        });
                                    },
                                    error: function (error) {
                                        alert(error.responseText);
                                    }
                                });

                                // hide alart (3s)
                                setTimeout(function () {
                                    $("#showmsg").hide();
                                }, 3000);

                            },
                            error: function (error) {
                                //debugger;
                                alert(error.responseText);
                            }
                        });
                    });
                    
                    
                    
                    
                      function deleteitem(id) {

            ///////Post 'Delete'
            $.ajax({
                type: 'Post',
                url: '/Api/api/Brand/' + id,
                dataType: 'json',
                success: function (data) {

                    // delete table tr
                    $('#tableItems').each(function () {
                        $("#tableItems tr").empty();

                    });


                    // add tr title
                    $("#tableItems").append(
                        "<tr><td><a class='glyphicon glyphicon-retweet' style='margin-right:5%'> </a> Brand Name </td><td> <a class='glyphicon glyphicon-retweet' style='margin-right:5%'> </a> Status </td> <td><a class='glyphicon glyphicon-retweet' style='margin-right:5%'> </a> Action </td></tr>");


                    // read Table ( new request [delete])
                    $.ajax({
                        type: 'Get',
                        url: '/Api/api/Brand',
                        dataType: 'json',
                        success: function (data) {
                            //debugger;
                            $.each(data, function (key, value) {
                                if (value.Status == "Active") {
                                    $("#tableItems").append(
                                        "<tr> <input type='hidden' class='valueid' value='" + value.ID + "'/> <td>" + value.Name + "</td > <td> <button type='button' class='btn btn-success  btn-xs'>" + value.Status + "</button></td> <td>  <button type='button' value='edit' class='glyphicon glyphicon-pencil' onClick=Edititem(\'" + value.ID + "\');></button>  <button type='button' class='glyphicon glyphicon-trash' value='Delete' onClick=deleteitem(\'" + value.ID + "\');></button>  </td>  </tr>");

                                }
                                else {
                                    $("#tableItems").append(
                                        "<tr> <input type='hidden' class='valueid' value='" + value.ID + "'/>  <td>" + value.Name + "</td > <td> <button type='button' class='btn btn-warning  btn-xs'>" + value.Status + "</button></td> <td>  <button type='button' value='edit' class='glyphicon glyphicon-pencil' onClick=Edititem(\'" + value.ID + "\');></button>  <button type='button' class='glyphicon glyphicon-trash' value='Delete' onClick=deleteitem(\'" + value.ID + "\');></button>  </td>  </tr>");

                                }
                            });
                        },
                        error: function (error) {
                            alert(error.responseText);
                        }
                    });
                },
                error: function (error) {
                    alert(error.responseText);
                }
            });
        }
        
        
        
        
                function Edititem(id) {

            debugger
            if (id != 0) {
                globalVariableid = id;


                //////show popup
                $('#ModalPopUp').modal().show();


                ////// edit popup
                $("#txtAddBrand").text("Update Prand");
                $("#update").show();
                $("#SaveChange").hide();


                //read table ( all id foreach for sql using ajax)
                $.ajax({
                    type: 'Get',
                    url: '/Api/api/Brand',
                    dataType: 'json',
                    success: function (data) {

                        // find obj.id = id
                        $.each(data, function (key, value) {

                            if (globalVariableid == value.ID) {
                                ///// Data obj from id send popup
                                $("#txtprandname").val(value.Name);
                                $("#txtselect").val(value.Status);
                            }

                        });


                    },
                    error: function (error) {
                        alert("error loop id");
                    }

                });


                //end if statment (id != 0)

            }



            //end function
        }



++                 window.location = '@Url.Action("Edit/id", "Order")'.replace("id",id);


-------------------------------------------------------
Angular


