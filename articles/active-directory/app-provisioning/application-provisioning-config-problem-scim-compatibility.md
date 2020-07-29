---
title: Známé problémy s dodržováním předpisů protokolu SCIM 2,0 – Azure AD
description: Řešení běžných problémů s kompatibilitou protokolů při přidávání aplikace mimo galerii, která podporuje SCIM 2,0 do Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 12/03/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: ae08589785d8a482801c71ce3641ba0d66d11133
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782258"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Známé problémy a řešení s dodržováním předpisů protokolu SCIM 2,0 ve službě Azure AD pro zřizování uživatelů

Azure Active Directory (Azure AD) může automaticky zřídit uživatele a skupiny do jakékoli aplikace nebo systému, který je frontou webové služby, s rozhraním definovaným v [systému pro specifikaci protokolu SCIM (Domain Identity Management) 2,0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Podpora Azure AD pro protokol SCIM 2,0 je popsaná v tématu [použití systému pro správu identit mezi doménami (SCIM) k automatickému zřizování uživatelů a skupin z Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md), které obsahují seznam specifických částí protokolu, které implementuje, aby bylo možné automaticky zřídit uživatele a skupiny z Azure AD až po aplikace podporující SCIM 2,0.

Tento článek popisuje aktuální a minulé problémy se službou zřizování uživatelů Azure AD při dodržování protokolu SCIM 2,0 a o tom, jak tyto problémy obejít.

> [!IMPORTANT]
> Poslední aktualizace služby Azure AD Provisioning Service SCIM Client se provedla 18. prosince 2018. Tato aktualizace řeší známé problémy s kompatibilitou, které jsou uvedeny v následující tabulce. Další informace o této aktualizaci najdete na stránce s nejčastějšími dotazy.

## <a name="scim-20-compliance-issues-and-status"></a>Problémy a stav dodržování předpisů SCIM 2,0

| **Problém dodržování předpisů pro SCIM 2,0** |  **Určí?** | **Opravit datum**  |  
|---|---|---|
| Azure AD vyžaduje, aby "/SCIM" v kořenu adresy URL koncového bodu SCIM aplikace.  | Yes  |  18. prosince 2018 | 
| Atributy rozšíření používají tečku "." před názvy atributů namísto dvojtečky ":" Notation. |  Yes  | 18. prosince 2018  | 
|  Žádosti o opravu atributů s více hodnotami obsahují neplatnou syntaxi filtru cesty. | Yes  |  18. prosince 2018  | 
|  Žádosti o vytvoření skupiny obsahují neplatný identifikátor URI schématu. | Yes  |  18. prosince 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Byly opravy služeb popsané automaticky u stávající aplikace SCIM?

Ne. Vzhledem k tomu, že by šlo o zásadní změnu aplikací SCIM, které byly kódované pro práci se starším chováním, se změny automaticky nepoužily u existujících aplikací.

Změny se aplikují na všechny nové [aplikace SCIM bez Galerie](../manage-apps/configure-single-sign-on-non-gallery-applications.md) nakonfigurované v Azure Portal po datu opravy.

Informace o tom, jak migrovat existující úlohu zřizování uživatelů tak, aby zahrnovala nejnovější opravy, najdete v další části.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Můžu migrovat existující úlohu zřizování uživatelů založenou na SCIM, aby zahrnovala nejnovější opravy služeb?

Ano. Pokud už tuto instanci aplikace používáte pro jednotné přihlašování a potřebujete migrovat existující úlohu zřizování, aby zahrnovala nejnovější opravy, postupujte podle níže uvedeného postupu. Tento postup popisuje, jak pomocí rozhraní Microsoft Graph API a Microsoft Graph API Exploreru odebrat starou úlohu zřizování z vaší stávající aplikace SCIM a vytvořit novou, která bude vykazovat nové chování.

> [!NOTE]
> Pokud je vaše aplikace stále ve vývoji a ještě nebyla nasazena pro jednotné přihlašování nebo zřizování uživatelů, nejjednodušší řešení je odstranit položku aplikace v části **Azure Active Directory > podnikové aplikace** v Azure Portal a jednoduše přidat novou položku pro aplikaci pomocí možnosti **vytvořit aplikaci > mimo galerii** . Toto je alternativa ke spuštění níže uvedeného postupu.
 
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

8. Zkopírujte výstup JSON z posledního kroku a uložte ho do textového souboru. Obsahuje všechny vlastní mapování atributů, které jste přidali do staré aplikace, a mělo by to být přibližně několik tisíc řádků formátu JSON.
9. Chcete-li odstranit úlohu zřizování, spusťte následující příkaz:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Spuštěním následujícího příkazu vytvořte novou úlohu zřizování s nejnovějšími opravami služeb.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. V části Výsledky posledního kroku zkopírujte úplný řetězec "ID", který začíná řetězcem "SCIM". Volitelně můžete znovu použít staré mapování atributů spuštěním následujícího příkazu, a to tak, že nahradíte [New-Job-ID] novým ID úlohy, kterou jste právě zkopírovali, a zadáte výstup JSON z kroku #7 jako text žádosti.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Vraťte se do prvního okna webového prohlížeče a vyberte kartu **zřizování** pro vaši aplikaci.
13. Ověřte konfiguraci a potom spusťte úlohu zřizování. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Můžu přidat novou aplikaci mimo galerii, která má staré chování při zřizování uživatelů?

Ano. Pokud jste nasadili aplikaci na staré chování, které existovalo před opravami, a potřebujete nasadit novou instanci této služby, postupujte podle níže uvedeného postupu. Tento postup popisuje, jak pomocí rozhraní Microsoft Graph API a Microsoft Graph API Exploreru vytvořit úlohu zřizování SCIM, která se projeví ve starém chování.
 
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

