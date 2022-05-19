//
//  LoginPage.swift
//  mfuture
//
//  Created by Muhammad Sigit Erwanto on 12/05/22.
//

import SwiftUI
import FirebaseCore
import FirebaseAnalytics
import FirebaseAuth

struct LoginPage: View {
    
    @State var email = ""
    @State var password = ""
    @State private var showingAlert = false

    let auth = Auth.auth()
    
    func login() {
        Auth.auth().signIn(withEmail: loginPageData.email, password: loginPageData.password) { (result, error) in
            if error != nil {
//                print(error?.localizedDescription ?? "")
                showingAlert = true
            } else {
                
                loginPageData.isLogin = true
            }
        }
    }
    
    func SignUp() {
        auth.createUser(withEmail: registerPageModel.email, password: registerPageModel.password) { (result, error) in
            if error != nil {
//                print(error?.localizedDescription ?? "")
                showingAlert = true
            } else {
                registerPageModel.isRegister = true
                registerPageModel.currentStep += 1
            }
        }
    }
    

    
    @StateObject var loginPageData : LoginPageModel = LoginPageModel()
    @StateObject var registerPageModel : RegisterPageModel = RegisterPageModel()
    
    var body: some View {
        VStack{
            Image("logo")
                .resizable()
                .aspectRatio(contentMode: .fit)
                .frame(width: 250, height: 80)
                .padding(.bottom, 50)
            
            Text(loginPageData.registerUser ? "Sign Up" : "Sign In")
                .font(.custom(poppins, size: 25).bold())
                .frame(maxWidth: .infinity, alignment: .leading)
            
            CustomTextField(icon: "person", hint: "Email", value: loginPageData.registerUser ?  $registerPageModel.email : $loginPageData.email, showPassword: .constant(false))
                .padding(.bottom, 10)
            
            CustomTextField(icon: "lock", hint: "Password", value: loginPageData.registerUser ?  $registerPageModel.password : $loginPageData.password, showPassword: $loginPageData.showPassword)
                .padding(.bottom, 10)
            
            if loginPageData.registerUser {
                CustomTextField(icon: "lock", hint: "Confirm Password", value: $registerPageModel.confirmPassword, showPassword: $loginPageData.showConfirmPassword)
                    .padding(.bottom, 10)
            }
            
            if !loginPageData.registerUser {
                Button {
                    loginPageData.showForgotPassword = true
                } label : {
                    Text("Forgot Password ?")
                        .font(.custom(poppins, size: 15))
                        .foregroundColor(Color("primaryColor"))
                        .frame(maxWidth: .infinity, alignment: .trailing)
                }
                .sheet(isPresented: $loginPageData.showForgotPassword) {
                    ForgotPasswordView(showForgotPassword: $loginPageData.showForgotPassword)
                }
            }
            
            Button {
                if loginPageData.registerUser {
                    withAnimation {
                            registerPageModel.registrationUser = true

                            SignUp()

                    }
                }else {
//                    loginPageData.signIn()
                    login()
                }
            } label: {
                Text(loginPageData.registerUser ? "Sign Up" : "Sign In")
                    .font(.custom(poppins, size: 15))
                    .fontWeight(.semibold)
                    .frame(width: 250, height: 40)
                    .foregroundColor(Color.white)
                    .background(Color("primaryColor"))
                    .cornerRadius(20)
                    .padding(.top, 30)
            }.alert("Your email or password is wrong", isPresented: $showingAlert) {
                Button("OK", role: .cancel) { }
            }
            
            HStack{
                Text(loginPageData.registerUser ? "Already have login and password ?" : "Don't have account ?")
                    .font(.custom(poppins, size: 15))
                    .foregroundColor(Color("secondaryColor2"))
                
                Button {
                    withAnimation {
                        loginPageData.registerUser.toggle()
                    }
                } label : {
                    Text(loginPageData.registerUser ? "Sign In" : "Sign Up")
                        .font(.custom(poppins, size: 15))
                        .fontWeight(.semibold)
                }
            }
            .padding(.top, 15)
            
            HStack {
                VStack {
                    Divider().background(Color("secondaryColor2"))
                }
                Text("OR")
                    .font(.custom(poppins, size: 15))
                    .fontWeight(.semibold)
                    .foregroundColor(Color("secondaryColor2"))
                VStack {
                    Divider().background(Color("secondaryColor2"))
                }
            }
            .padding()
            
            HStack(spacing: 50) {
                Button(action: {
                    print("gmail")
                }) {
                    Image("gmail")
                }
                
                Button(action: {
                    print("facebook")
                }) {
                    Image("facebook")
                }
                
                Button(action: {
                    print("apple")
                }) {
                    Image("apple")
                }
                
            }
            
        }
        .padding(.all, 30)
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(Color.white)
        .overlay(
            Group {
                if loginPageData.registrationUser {
                    RegisterPage(registrationUser: $loginPageData.registrationUser).transition(.move(edge: .trailing))
                }
            }
        )
    }

    
    @ViewBuilder
    func CustomTextField(icon: String, hint: String, value: Binding<String>, showPassword: Binding<Bool>) -> some View {
        VStack(spacing: 15) {
            HStack {
                Image(systemName: icon)
                    .foregroundColor(.secondary)
                
                if hint.contains("Password") && !showPassword.wrappedValue {
                    SecureField(hint, text: value)
                }else {
                    TextField(hint, text: value)
                }
                
                if hint.contains("Password") {
                    Button(action: { showPassword.wrappedValue.toggle()}) {
                        Image(systemName: !showPassword.wrappedValue ? "eye.slash" : "eye")
                            .foregroundColor(.secondary)
                    }
                }
            }
            
            Divider()
                .background(Color.black.opacity(0.4))
        }
    }
}

struct LoginPage_Previews: PreviewProvider {
    static var previews: some View {
        LoginPage().previewDevice("iPhone 8")
        LoginPage().previewDevice("iPhone 12")
    }
}
