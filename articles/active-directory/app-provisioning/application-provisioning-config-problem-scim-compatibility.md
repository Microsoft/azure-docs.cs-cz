---
title: Známé problémy se systémem pro správu identit mezi doménami (SCIM) 2,0 – Azure AD
description: Řešení běžných problémů s kompatibilitou protokolů při přidávání aplikace mimo galerii, která podporuje SCIM 2,0 do Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d13629b4cb05995b9652e862f769a0ffcae30a8c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256894"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Známé problémy a řešení s dodržováním předpisů protokolu SCIM 2,0 ve službě Azure AD pro zřizování uživatelů

Azure Active Directory (Azure AD) může automaticky zřídit uživatele a skupiny do jakékoli aplikace nebo systému, který je frontou webové služby, s rozhraním definovaným v [systému pro specifikaci protokolu SCIM (Domain Identity Management) 2,0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Podpora Azure AD pro protokol SCIM 2,0 je popsaná v tématu [použití systému pro správu identit mezi doménami (SCIM) k automatickému zřizování uživatelů a skupin z Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md), které obsahují seznam specifických částí protokolu, které implementuje, aby bylo možné automaticky zřídit uživatele a skupiny z Azure AD až po aplikace podporující SCIM 2,0.

Tento článek popisuje aktuální a minulé problémy se službou zřizování uživatelů Azure AD při dodržování protokolu SCIM 2,0 a o tom, jak tyto problémy obejít.

## <a name="understanding-the-provisioning-job"></a>Princip zřizování úlohy
Služba zřizování používá koncept úlohy pro práci s aplikací. JobID lze najít v [indikátoru průběhu](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar). Všechny nové zřizovací aplikace se vytvoří s jobID začínajícím řetězcem "SCIM". Úloha SCIM představuje aktuální stav služby. Starší úlohy mají ID "customappsso". Tato úloha představuje stav služby v 2018. 

Pokud používáte aplikaci v galerii, úloha obvykle obsahuje název aplikace (např. snowFlake lupy, datacihly atd.). Tuto dokumentaci můžete přeskočit při použití aplikace Galerie. Toto platí hlavně pro aplikace mimo galerii s jobID SCIM nebo customAppSSO.

## <a name="scim-20-compliance-issues-and-status"></a>Problémy a stav dodržování předpisů SCIM 2,0
V následující tabulce je jakákoli položka označená jako pevná, což znamená, že v úloze SCIM lze najít správné chování. Pracovali jsme na tom, abychom zajistili zpětnou kompatibilitu pro změny, které jsme udělali. Nedoporučujeme ale implementovat staré chování. Pro všechny nové implementace a aktualizace stávajících implementací doporučujeme použít nové chování.

> [!NOTE]
> Pro změny provedené v 2018 se můžete vrátit zpátky k customappsso chování. U změn provedených od 2018 můžete použít adresy URL a vrátit se k předchozímu chování. Pracovali jsme na tom, abychom zajistili zpětnou kompatibilitu pro změny, které jsme provedli, díky tomu, abyste se vrátili zpátky k původnímu jobID nebo pomocí příznaku. Jak už ale jsme uvedli, nedoporučujeme implementovat staré chování. Pro všechny nové implementace a aktualizace stávajících implementací doporučujeme použít nové chování.

| **Problém dodržování předpisů pro SCIM 2,0** |  **Určí?** | **Opravit datum**  |  **Zpětná kompatibilita** |
|---|---|---|
| Azure AD vyžaduje, aby "/SCIM" v kořenu adresy URL koncového bodu SCIM aplikace.  | Ano  |  18. prosince 2018 | downgrade na customappSSO |
| Atributy rozšíření používají tečku "." před názvy atributů namísto dvojtečky ":" Notation. |  Ano  | 18. prosince 2018  | downgrade na customappSSO |
| Žádosti o opravu atributů s více hodnotami obsahují neplatnou syntaxi filtru cesty. | Ano  |  18. prosince 2018  | downgrade na customappSSO |
| Žádosti o vytvoření skupiny obsahují neplatný identifikátor URI schématu. | Ano  |  18. prosince 2018  |  downgrade na customappSSO |
| Aktualizace chování opravy, aby se zajistilo dodržování předpisů (např. aktivní jako logická a správná odebrání členství ve skupině) | Ne | Bude doplněno| použít příznak náhledu |

## <a name="flags-to-alter-the-scim-behavior"></a>Příznaky pro změnu chování SCIM
Pomocí příznaků níže v adrese URL klienta aplikace můžete změnit výchozí chování klienta SCIM.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="SCIM příznaky na pozdější chování.":::

* Pomocí následující adresy URL aktualizujte chování opravy a zajistěte, aby SCIM dodržování předpisů (třeba aktivní jako logická a správná odebrání členství ve skupině). Toto chování je nyní k dispozici pouze při použití příznaku, ale během několika následujících měsíců se stane výchozím chováním. Poznámka: Tento příznak Preview aktuálně nefunguje se zřizováním na vyžádání. 
  * **Adresa URL (kompatibilní s SCIM):** AzureAdScimPatch062020
  * **Odkazy na dokumenty RFC SCIM:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **Předvídatelně**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **Adresa URL downgradu:** Jakmile se nové chování kompatibilní s SCIM nastaví jako výchozí v aplikaci mimo galerii, můžete pomocí následující adresy URL vrátit se k původnímu chování, které nedodržuje předpisy SCIM: AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>Upgrade ze starší úlohy customappsso na úlohu SCIM
Následující postup odstraní existující úlohu customappsso a vytvoří novou úlohu SCIM. 
 
1. Přihlaste se k Azure Portal na https://portal.azure.com .
2. V Azure Portal oddílu **Azure Active Directory > podnikové aplikace** vyhledejte a vyberte existující aplikaci SCIM.
3. V části **Properties (vlastnosti** ) vaší existující aplikace SCIM zkopírujte **ID objektu**.
4. V novém okně webového prohlížeče klikněte na https://developer.microsoft.com/graph/graph-explorer a přihlaste se jako správce tenanta Azure AD, do kterého se vaše aplikace přidala.
5. V Průzkumníku grafů spusťte následující příkaz a vyhledejte ID vaší úlohy zřizování. Nahraďte "[Object-ID]" ID objektu služby (ID objektu) zkopírovaným z třetího kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Získat úlohy](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Získat úlohy") 


6. Ve výsledcích zkopírujte úplný řetězec "ID", který začíná buď "customappsso", nebo "SCIM".
7. Spusťte následující příkaz, který načte konfiguraci mapování atributů, takže můžete vytvořit zálohu. Použijte stejné [Object-ID] jako dřív a nahraďte [Job-ID] ID úlohy zřizování zkopírované z posledního kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Získat schéma](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Získat schéma") 

8. Zkopírujte výstup JSON z posledního kroku a uložte ho do textového souboru. JSON obsahuje jakákoli vlastní mapování atributů, která jste přidali do staré aplikace, a měla by být přibližně několik tisíc řádků JSON.
9. Chcete-li odstranit úlohu zřizování, spusťte následující příkaz:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Spuštěním následujícího příkazu vytvořte novou úlohu zřizování s nejnovějšími opravami služeb.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. V části Výsledky posledního kroku zkopírujte úplný řetězec "ID", který začíná řetězcem "SCIM". Volitelně můžete znovu použít staré mapování atributů spuštěním následujícího příkazu a nahradit [New-Job-ID] novým ID úlohy, které jste zkopírovali, a zadáním výstupu JSON z kroku #7 jako textu žádosti.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Vraťte se do prvního okna webového prohlížeče a vyberte kartu **zřizování** pro vaši aplikaci.
13. Ověřte konfiguraci a potom spusťte úlohu zřizování. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>Downgrading z úlohy SCIM na úlohu customappsso (nedoporučuje se)
 Umožníme vám navrátit se k původnímu chování, ale nedoporučujeme ho, protože customappsso nepřináší výhody z některých aktualizací, které provedeme, a nemusí být podporované trvale. 

1. Přihlaste se k Azure Portal na https://portal.azure.com .
2. v části **Azure Active Directory > podnikové aplikace > vytvořit aplikaci** v Azure Portal vytvořte novou aplikaci **mimo galerii** .
3. V části **vlastnosti** vaší nové vlastní aplikace zkopírujte **ID objektu**.
4. V novém okně webového prohlížeče klikněte na https://developer.microsoft.com/graph/graph-explorer a přihlaste se jako správce tenanta Azure AD, do kterého se vaše aplikace přidala.
5. V Průzkumníku grafů spusťte níže uvedený příkaz pro inicializaci konfigurace zřizování pro vaši aplikaci.
   Nahraďte "[Object-ID]" ID objektu služby (ID objektu) zkopírovaným z třetího kroku.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Vraťte se do prvního okna webového prohlížeče a vyberte kartu **zřizování** pro vaši aplikaci.
7. Dokončete konfiguraci zřizování uživatelů obvyklým způsobem.


## <a name="next-steps"></a>Další kroky
[Další informace o zřizování a zrušení zřizování pro aplikace SaaS](user-provisioning.md)
