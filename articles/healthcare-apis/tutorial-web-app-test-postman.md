---
title: Kurz k webové aplikaci – testování připojení k Azure API pro FHIR
description: Tyto kurzy vás seznámí s příkladem nasazení jednoduché webové aplikace. Tato část kurzu vás provede testováním připojení k serveru FHIR pomocí nástroje post.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023308"
---
# <a name="testing-the-fhir-api"></a>Testování rozhraní FHIR API
V předchozích dvou krocích jste nasadili rozhraní Azure API pro FHIR a zaregistrovali jste klientskou aplikaci. Nyní jste připraveni otestovat, že vaše rozhraní Azure API pro FHIR je nastaveno pomocí klientské aplikace. 

## <a name="retrieve-capability-statement"></a>Načíst příkaz schopnosti
Nejdřív získáte příkaz Capability pro vaše rozhraní API Azure pro FHIR. 
1. Otevřít po
1. Pomocí příkazu GET https://. azurehealthcareapis.com/metadata načtěte příkaz Capability \<FHIR-SERVER-NAME> . Na obrázku pod názvem FHIR serveru je **fhirserver**.

![Příkaz Capability](media/tutorial-web-app/postman-capability-statement.png)

Pak se pokusíme načíst pacienta. Pokud chcete načíst pacienta, zadejte GET https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com/Patient. Zobrazí se chyba neautorizované na 401. Tato chyba je způsobená tím, že jste neprokázali, že byste měli mít přístup k datům pacientům.

## <a name="get-patient-from-fhir-server"></a>Získat pacienta ze serveru FHIR
![Neúspěšný pacient](media/tutorial-web-app/postman-patient-authorization-failed.png)

Aby bylo možné získat přístup, potřebujete přístupový token.
1. V poli pro odeslání vyberte **autorizaci** a nastavte typ na **OAuth 2.0** .
1. Vybrat **získat nový přístupový token**
1. Vyplňte pole a vyberte **token žádosti**. Níže můžete zobrazit hodnoty pro každé pole v tomto kurzu.

|Pole                |Hodnota                                                               |
|---------------------|--------------------------------------------------------------------|
|Název tokenu           |Název tokenu                                               |
|Typ udělení           |Autorizační kód                                                  |
|Adresa URL zpětného volání         |https://www.getpostman.com/oauth2/callback                          |
|Ověřovací adresa URL             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/OAuth2/? Resource = https:// \<FHIR-SERVER-NAME> . azurehealthcareapis.com|
|Adresa URL přístupového tokenu     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/token|
|ID klienta            |ID klienta, které jste zkopírovali během předchozích kroků             |
|Tajný klíč klienta        |\<BLANK>                                                            |
|Rozsah                |\<BLANK>                                                            |
|State                |1 234                                                                |
|Ověření klienta|Poslat přihlašovací údaje klienta v těle                                     |

4. Přihlaste se pomocí svých přihlašovacích údajů a vyberte **přijmout** .
1. Posuňte se na výsledek a vyberte **použít token** .
1. Vyberte **Odeslat** znovu v horní části a tentokrát byste měli získat ![ pacienta s úspěšným výsledkem.](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Odeslání pacienta do FHIR serveru
Teď máte přístup, můžete vytvořit nového pacienta. Tady je Ukázka jednoduchého pacienta, který můžete přidat do serveru FHIR. Níže uvedený kód zadejte do části **text** příspěvku.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Tento příspěvek vytvoří nový pacient na serveru FHIR s názvem James Tiberious Kirka.
![Odeslání pacienta](media/tutorial-web-app/postman-post-patient.png)

Pokud převedete výše uvedený krok získat pacient a znovu načíst pacient, zobrazí se ve výstupu pole James Tiberious Kirka.

## <a name="troubleshooting-access-issues"></a>Řešení potíží s přístupem
Pokud jste narazili na problémy v některém z těchto kroků, Projděte si dokumenty, které jsme nastavili na Azure Active Directory a rozhraní API Azure pro FHIR. 

* [Azure AD a Azure API pro FHIR](azure-ad-hcapi.md) – tento dokument popisuje některé základní principy Azure Active Directory a způsob, jakým komunikuje s rozhraním API Azure pro FHIR.
* [Ověření přístupového tokenu](azure-ad-hcapi-token-validation.md) – Tato příručka poskytuje konkrétnější podrobnosti o ověřování přístupového tokenu a krocích, které je potřeba vyřešit při řešení problémů s přístupem.

## <a name="next-steps"></a>Další kroky
Teď, když se můžete úspěšně připojit ke klientské aplikaci, jste připraveni napsat webovou aplikaci.

>[!div class="nextstepaction"]
>[Zápis webové aplikace](tutorial-web-app-write-web-app.md)



