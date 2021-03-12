---
title: Kurz k webové aplikaci – zápis webové aplikace
description: Tento kurz vás provede příkladem nasazení jednoduché webové aplikace. V této části kurzu se seznámíte s psaním webové aplikace.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018471"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>Zápis webové aplikace Azure pro čtení dat FHIR
Teď, když se můžete připojit k serveru FHIR a ODESÍLAT data, jste připraveni napsat webovou aplikaci, která bude číst data FHIR. V tomto posledním kroku kurzu Vás provedeme zápisem a přístupem k webové aplikaci.

## <a name="create-web-application"></a>Vytvoření webové aplikace
V Azure vyberte **vytvořit prostředek** a vyberte **Webová aplikace**. Nezapomeňte pojmenovat webovou aplikaci, ať už jste zadali v identifikátoru URI přesměrování pro vaši klientskou aplikaci, nebo se vraťte zpátky a aktualizujte identifikátor URI přesměrování s novým názvem. 

![Vytvoření webové aplikace](media/tutorial-web-app/create-web-app.png)

Jakmile je webová aplikace k dispozici, **Přejít na prostředek**. V části vývojové nástroje na pravé straně vyberte **Editor služby App Service (Preview)** a pak vyberte **Přejít**. Když vyberete přejít, otevře se Editor služby App Service. Klikněte pravým tlačítkem myši na šedé místo pod položkou *prozkoumat* a vytvořte nový soubor s názvem **index.html**.

Níže je kód, který můžete zadat do **index.html**. Budete muset aktualizovat tyto položky:
* **ClientID** – aktualizace s ID klientské aplikace Toto ID bude stejné ID, které jste si vyžádali při načítání tokenu.
* **autorita** – aktualizace s ID TENANTA Azure AD
* **FHIRendpoint** – aktualizujte FHIRendpoint tak, aby měl název vaší služby FHIR.
* **obory** – aktualizace, aby odrážely úplnou adresu URL pro vaši cílovou skupinu

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

Odsud se můžete vrátit k prostředku webové aplikace a otevřít adresu URL, která se nachází na stránce Přehled. Přihlaste se, abyste viděli pacienta Tiberious Kirka, který jste vytvořili dříve.

## <a name="next-steps"></a>Další kroky
Úspěšně jste nasadili rozhraní API Azure pro FHIR, zaregistrovali jste veřejnou klientskou aplikaci, otestovali jste přístup a vytvořili malou webovou aplikaci. V dalším kroku se podívejte na rozhraní API Azure pro FHIR podporované funkce.

>[!div class="nextstepaction"]
>[Podporované funkce](fhir-features-supported.md)





