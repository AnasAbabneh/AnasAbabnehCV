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

Image

        [HttpPost]
        [Route("UploadFile")]
        public string UploadFile()
        {
            HttpPostedFile file = HttpContext.Current.Request.Files[0];
            string roothPath = "~/UploadedImages/" + file.FileName;
            file.SaveAs(@"C:/inetpub/wwwroot/InventorySystem/InventoryWebApi/UploadedImages/" + file.FileName);
            string Path = roothPath.Substring(1);
            return Path;
           
        }
Cross  

 Install-Package Microsoft.AspNet.WebApi.Cors
 using System.Web.Http;
 using System.Web.Http.Cors;
 EnableCorsAttribute cors = new EnableCorsAttribute("http://localhost:4200", "*", "*");
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

file upload

            $("#fileupload").change(function () {
                debugger;
                var files = $(this).get(0).files;
                var formData = new FormData();
                formData.append(files[0].name, files[0]);

                $.ajax({
                    url: "/Api/UploadFile",
                    method: "POST",
                    data: formData,
                    processData: false,
                    contentType: false,
                    success: function (data) {
                        debugger;
                        $("#imgProfilPhoto").attr("src", "/InventorySystem/InventoryWebApi" + data); 
                    },
                    error: function (data) {
                        alert(" error Upload image !")
                    }
                });
            });

                });
                
read image
 <td> <img id='image' style='width:50px; height:50px;' src='" + value.ImagURL +"' />   </td>
Strongly Query
                window.location = '@Url.Action("Edit/id", "Order")'.replace("id",id);


-------------------------------------------------------
Angular

ng new Name
ng g c NameComponant
ng serve [open]
npm install --save bootstrap@3








Route 

import { RouterModule, Routes } from '@angular/router';


const MyAppRoute:Routes=[
  {path:'X',component:XComponant},
  {path:'X1',component:X1cpmponant},
  

];

imports: [
    BrowserModule,
    HttpClientModule,
    FormsModule,
    RouterModule.forRoot(MyAppRoute)
  ],
 
 providers: [httpService,X,X1,X2],
  
  
    <li><a routerLink='Category'> Category </a></li>
    <li><a routerLink='Stor'> Stor </a></li>
    <router-outlet></router-outlet>      
         
         
         
--------------------------------------------
Class [ All , HttpServer , DTO ]

export class X
{
     ID : number;
     Name : string;
     Status : string;

}

import {  } from "../";

export class VMA{
     count:number;
     listattr:;
}


import { HttpClient } from '@angular/common/http';
import {Injectable} from '@angular/core'
import { Observable } from 'rxjs';
import { X } from './X';

     @Injectable()

     export class httpService{
      formData = new FormData();

    constructor(private httpcli:HttpClient){}
   
    //List Product Api
    loadallProduct():Observable<any>{
        return this.httpcli.get('http://localhost/Api/api/Product');
     }
 
     //Add Product Api
     SaveProduct(prod:Product){
         this.httpcli.post('http://localhost/Api/api/Product',prod).subscribe();
     }
 
     //delete Product Api 
     deleteProduct(id:number){
         this.httpcli.post('http://localhost/Api/api/Product/'+id,{}).subscribe();
     }
 
      //Update Product Api
      UpdateProduct(prod:Product){
         this.httpcli.post('http://localhost/Api/Product/Update',prod).subscribe();
     }

      //Update File Api
      ProductFile(formData){
        this.httpcli.post('http://localhost/Api/UploadFile',formData).subscribe();
    }
-------------------------------------------------
TS + Html

1-show hid dropdown List
pop:boolean=true;
MenueValue:any={  product:false,  order:false,}
togg(name:string){this.MenueValue[name] =!this.MenueValue[name];  }
sidebar(){this.pop =!this.pop;}
          
          <li>
                        <a class="dropdown-toggle" (click)='togg("product");'  data-toggle="collapse"><i class="glyphicon glyphicon-triangle-bottom"></i> Products</a>
                         <ul class="list-unstyled"  *ngIf="MenueValue.product">
                              <li>
                             <a routerLink='AddProduct'> AddProduct </a>
                             <a routerLink='ManageProduct'> ManageProduct </a>
                          </li>
                      </ul>
                  </li>
                  
                  
2- branx

x.ts

                import { Component, ElementRef, OnInit, ViewChild } from '@angular/core';
                import { NgForm } from '@angular/forms';
               import { Subscriber } from 'rxjs';
               import { Brand } from '../DataClases/Brand';
               import { httpService } from '../DataClases/httpService';

             @Component({
             selector: 'app-add-brand',
              templateUrl: './add-brand.component.html',
              styleUrls: ['./add-brand.component.css']
               })
               export class AddBrandComponent implements OnInit {
  
             //name:string = 'anas';
             pop:boolean=false;
             btnSave:boolean=true;
             btnUpdate:boolean=false;
             txterrormsg:boolean=false;

             listbran:Brand[]; 
  
             @ViewChild('F',{static:false}) FormBrand:NgForm;

             divSucc:boolean=false;

             constructor(private brandSer:httpService) { }
             bran = new Brand();
            ngOnInit():void {
            this.load();  
             }

             // Get api List
           load(){
    this.brandSer.loadall().subscribe(data=>{this.listbran=data;});
             }
 
       // set value null
        Add(){
     this.FormBrand.form.patchValue({
      "txtbrandname":"",
      "ddlbrandstatus":"Active"
    });
     }

       // on click save band
         onSaveBrand(){
    debugger;
    this.bran.Name=this.FormBrand.value.txtbrandname;
    this.bran.Status=this.FormBrand.value.ddlbrandstatus;
    if(this.bran.Name != ""){
      this.brandSer.Save(this.bran);
      this.pop=false;
      this.divSucc=true;
      setTimeout(() => {
        this.divSucc=false;
      }, 3000);
      setTimeout(() => {
        this.load(); 
      }, 300);
  
    }
    else{
      this.txterrormsg=true;
      setTimeout(() => {
        this.txterrormsg=false;
      }, 3000);
    }
   
         }

          // delete brand
       delete(id){
        this.brandSer.delete(id);
       setTimeout(() => {
        this.load();  
      }, 300);
       }


        globalId:number;

       // edit brand
       edit(item){
       debugger;
       this.pop=true;
       this.btnSave=false;
       this.btnUpdate=true;
       this.globalId=item.ID;

       this.FormBrand.form.patchValue({
       "txtbrandname":item.Name,
       "ddlbrandstatus":item.Status
       });

       }

       // click Update brand
       onUpdateBrand(){
       this.pop=true;
       this.btnSave=false;
       this.btnUpdate=true;
       this.bran.Name=this.FormBrand.value.txtbrandname;
       this.bran.Status=this.FormBrand.value.ddlbrandstatus;
       this.bran.ID=this.globalId;

    if(this.bran.Name != ""){
      this.brandSer.Update(this.bran);
      this.pop=false;
      setTimeout(() => {
      this.load(); 
      }, 300);
  
      }
   
      }

      }
      private orderRef:Order, private rout:Router
      // edit product
      edit(item){
      debugger;
      this.orderRef.ID=item.ID;
      this.orderRef.Discount=item.Discount;
      this.orderRef.GrossAmount=item.GrossAmount;
      this.orderRef.Name=item.Name;
      this.orderRef.NetAmount=item.NetAmount;
      this.orderRef.Address=item.Address;
      this.orderRef.AmountPrice=item.AmountPrice;

        this.rout.navigate(['/AddOrder'],{queryParams:{x:item.ID}});
      }

         + this.OrderSer.loadallProduct().subscribe(data=>{this.listprod=data;});
         + private orderID:ActivatedRoute /   valueIdUpdate:any = this.orderID.snapshot.queryParams.x;
        
        
        //Image File
         secimag:string;
           imgfile:File;

    changeImage(event:any){
      debugger;
      if(event.target.files[0] != null && event.target.files.length>0){
        this.imgfile=event.target.files[0];
        var formData = new FormData();
        formData.append(event.target.files[0].name, event.target.files[0]);
        this.ProductSer.ProductFile(formData);
        console.log(this.imgfile);
        this.loadimg=true;
        setTimeout(() => {
          this.secimag="http://localhost/UploadedImages/" + this.imgfile.name;
          console.log(this.secimag);

      }, 300);

      }else{
        this.imgfile=null;
        console.log("close imge");
      }
      
    }
    
3- html

      <div class="row ">
          <div class="col-lg-12 col-xs-12">
              <div class="modal"  data-backdrop="static" id="ModalPopUp" tabindex="-1" [ngClass]="{'show':pop==true}">
                  <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button id="DivClose" type="button" class="close" (click)="pop=false" data-dismiss="modal">&times;</button>
                        <h4 class="modal-title" id="txtAddBrand" > Add Brand</h4>
                    </div>
                    <div class="modal-body">


                        <form #F="ngForm">
                            <div class="form-group">
                                <label for="txtprandname" id="txtnameprandlable" >prand name</label>
                                <input type="text" class="form-control" placeholder="prandname" id="txtprandname" name="txtbrandname"  [ngStyle]="{'box-shadow':txtprandname !== ''? ' ' : '10px 10px grey' }"  ngModel />
                                <label *ngIf="txterrormsg" style="color: red;"> Please Entr Name</label>

                            </div>
                            <div class="form-group">
                                <label for="txtusername" id="Namestats">Stats</label>
                                <select class="selectpicker form-control" id="txtselect" name="ddlbrandstatus" ngModel >
                                    <option>Active</option>
                                    <option>inActive</option>
                                </select>

                            </div>
                        </form>
                    </div>

                    <div class="modal-footer">
                        <button class="btn btn-primary" (click)="onSaveBrand()" *ngIf="btnSave"> Save Change</button>
                        <button class="btn btn-primary"  id="update" *ngIf="btnUpdate" (click)="onUpdateBrand()"> update</button>

                        
                        <button class="btn btn-default" id="btnCloseModal" (click)="pop=false"> close</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
      </div>


      <div *ngIf="divSucc" class=" col-lg-8 col-xs-12 alert alert-success " id="showmsg" role="alert" >
    Succesfully Created
      </div>


      <div class="container ">

    <div class="row ">
        <div class="col-lg-12 col-xs-6">
            <span class="text-body" style="font-size:20px"> Manage </span>
            <span class="text-muted" style="font-size:10px"> Brandes </span>
        </div>

    </div>
    <div class="row ">
        <div class="col-lg-12 col-xs-12" style="padding-bottom:15px">
            <button type="button" name="btnLink" id="btnLink" class="btn btn-primary" (click)="pop=true;btnUpdate=false;btnSave=true;Add()">Add Brand</button>
        </div>
    </div>


    <div class="row  ContainerBody">
        <div class="col-lg-12 col-xs-12 containerHeader">
            Manage Brands
        </div>
        <div>

            <table class="table table-striped table-hover GridList" id="tableItems">

                <tr style=" font-weight:bold;">
                    <td><a class="glyphicon glyphicon-retweet" style="margin-right:5%"> </a> Brand Name </td>
                    <td> <a class="glyphicon glyphicon-retweet" style="margin-right:5%"> </a> Status </td>
                    <td><a class="glyphicon glyphicon-retweet" style="margin-right:5%"> </a> Action </td>
                </tr>
                <tr *ngFor="let item of listbran" style=" font-weight:lighter;" >
                    <td> {{item.Name}} </td>
                    <td style="color: honeydew;"> <button class="btn btn-xs" [ngStyle]="{'background-color':item.Status =='Active'? '#006400' : '#FF8C00'}"> {{item.Status}} </button></td>
                    <td> <a class="btn btn-sm " (click)="delete(item.ID)"> <i class='glyphicon glyphicon-trash'></i> </a> <a class="btn btn-sm " (click)="edit(item)">  <i class='glyphicon glyphicon-pencil' > </i></a></td>
             
                </tr>

            </table>
        </div>
    </div>
</div>

   <!-- Select prodact -->
        <div class="col-lg-5 col-xs-12">


            <label for="ddlproductSelect" id="ddlproductSelect">product</label>
            <select  class="form-control" name="txtproduct" (change)="onChangeProdact()"  ngModel> 
                <option value="" disabled selected>Select Store</option>
                <option *ngFor="let Product of listprod" [value]="Product.ID" >
                  {{Product.Name}} 
                </option>
              </select>
        </div>
        
        //image read
             <tr *ngFor="let item of listProduct" style=" font-weight:lighter;" >
                    <td> <img id='image' style='width:50px; height:50px;' [src]="item.ImagURL" />   </td>
                   
                   
         //image Add
                       <div class="row" style="padding:15px">
        <div class="col-lg-12 col-xs-12 ">
            <label> image </label>
        </div>
        <div class="col-lg-12 col-xs-12 ">
            <span style="padding:10px">
                <!-- <label for="myfile"><i class="glyphicon glyphicon-tags"></i></label>  -->
                <input type="file" class="btn btn-default btn-sm" name="pictrue" (change)="changeImage($event)" id="fileupload" ngModel/>
                
            </span>


            <span style="padding:10px">

                <!--<label for="myfile"><i class=" glyphicon glyphicon-folder-open"></i></label>
                <img id="imgProfilPhoto" style="width:150px; height:150px;" /> -->
                <img *ngIf="loadimg" style="width:150px; height:150px;" [src]="secimag" />

            </span>

        </div>
    </div>
