---
title: Známé problémy s dodržováním protokolu SCIM 2.0 – Azure AD
description: Jak vyřešit běžné problémy s kompatibilitou protokolů při přidávání aplikace bez galerie, která podporuje SCIM 2.0 do Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff0d4f8f0062c00351b60174c63d9c19bdfa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522930"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Známé problémy a řešení s dodržováním protokolu SCIM 2.0 služby Azure AD User Provisioning

Azure Active Directory (Azure AD) může automaticky zřazovat uživatele a skupiny do libovolné aplikace nebo systému, který je fronted webové služby s rozhraním definovaným ve [specifikaci protokolu System for Cross-Domain Identity Management (SCIM) 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Podpora Azure AD pro protokol SCIM 2.0 je popsána v [části Použití systému pro správu identit mezi doménami (SCIM) k automatickému zřizování uživatelů a skupin z Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md), které uvádí konkrétní části protokolu, který implementuje, aby se automaticky zřizovaly uživatelé a skupiny z Azure AD do aplikací, které podporují SCIM 2.0.

Tento článek popisuje aktuální a minulé problémy s dodržováním protokolu SCIM 2.0 uživatelem Služby Azure AD a jak tyto problémy vyřešit.

> [!IMPORTANT]
> Poslední aktualizace klienta SCIM služby zřizování uživatelů Azure AD byla provedena 18. Tato aktualizace řešila známé problémy s kompatibilitou uvedené v následující tabulce. Další informace o této aktualizaci naleznete v nejčastějších dotazech níže.

## <a name="scim-20-compliance-issues-and-status"></a>Problémy a stav dodržování předpisů scim 2.0

| **Problém s dodržováním předpisů scim 2.0** |  **Dlouhodobého?** | **Datum opravy**  |  
|---|---|---|
| Azure AD vyžaduje "/scim" být v kořenovém adresáři adresy URL koncového bodu SCIM aplikace  | Ano  |  18. prosince 2018December 18, 2018 | 
| Atributy rozšíření používají tečku "." zápis před názvy atributů namísto zápisu dvojtečky |  Ano  | 18. prosince 2018December 18, 2018  | 
|  Opravy požadavků na atributy s více hodnotami obsahují neplatnou syntaxi filtru cesty | Ano  |  18. prosince 2018December 18, 2018  | 
|  Požadavky na vytvoření skupiny obsahují neplatný identifikátor URI schématu | Ano  |  18. prosince 2018December 18, 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Byly popsané opravy služeb automaticky použity v mé již existující aplikaci SCIM?

Ne. Vzhledem k tomu, že by to představovalo narušující změnu aplikací SCIM, které byly kódovány pro práci se starším chováním, změny nebyly automaticky použity pro existující aplikace.

Změny se použijí na všechny nové [aplikace SCIM bez galerie](../manage-apps/configure-single-sign-on-non-gallery-applications.md) nakonfigurované na webu Azure Portal po datu opravy.

Informace o tom, jak migrovat již existující úlohu zřizování uživatelů tak, aby obsahovala nejnovější opravy, naleznete v další části.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Můžu migrovat existující úlohu zřizování uživatelů založené na SCIM tak, aby zahrnovala nejnovější opravy služeb?

Ano. Pokud již používáte tuto instanci aplikace pro jednotné přihlašování a potřebujete migrovat existující úlohu zřizování, aby zahrnovala nejnovější opravy, postupujte podle následujícího postupu. Tento postup popisuje, jak pomocí rozhraní Microsoft Graph API a průzkumníka rozhraní Microsoft Graph API odebrat staré úlohy zřizování z existující aplikace SCIM a vytvořit novou, která vykazuje nové chování.

> [!NOTE]
> Pokud je vaše aplikace stále ve vývoji a ještě nebyla nasazena pro jednotné přihlašování nebo zřizování uživatelů, nejjednodušším řešením je odstranit položku aplikace v části **Azure Active Directory > Enterprise Applications** na webu Azure Portal a jednoduše přidat novou položku pro aplikaci pomocí **možnosti Vytvořit aplikaci > bez galerie.** Jedná se o alternativu ke spuštění níže uvedeného postupu.
 
1. Přihlaste se https://portal.azure.comk portálu Azure na adrese .
2. V části **Azure Active Directory > Podnikové aplikace** na webu Azure Portal vyhledejte a vyberte stávající aplikaci SCIM.
3. V části **Vlastnosti** existující aplikace SCIM zkopírujte **ID objektu**.
4. V novém okně webového https://developer.microsoft.com/graph/graph-explorer prohlížeče přejděte na a přihlaste se jako správce pro klienta Azure AD, kde se vaše aplikace přidá.
5. V Průzkumníku grafů spusťte následující příkaz a vyhledejte ID úlohy zřizování. Nahraďte "[id objektu]" ID instancí (ID objektu) zkopírované ze třetího kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![Získejte pracovní místa](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "Získejte pracovní místa") 


6. Ve výsledcích zkopírujte úplný řetězec "ID", který začíná buď "customappsso" nebo "scim".
7. Spusťte níže uvedený příkaz a načtěte konfiguraci mapování atributů, abyste mohli vytvořit zálohu. Použijte stejné [objekt id] jako dříve a nahraďte [job-id] ID úlohy zřizování zkopírované z posledního kroku.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![Získat schéma](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "Získat schéma") 

8. Zkopírujte výstup JSON z posledního kroku a uložte jej do textového souboru. To obsahuje všechny vlastní mapování atributů, které jste přidali do staré aplikace a by měla být přibližně několik tisíc řádků JSON.
9. Spusťte následující příkaz, chcete-li odstranit úlohu zřizování:
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Spusťte níže uvedený příkaz a vytvořte novou úlohu zřizování, která obsahuje nejnovější opravy služeb.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. Ve výsledcích posledního kroku zkopírujte úplný řetězec "ID", který začíná řetězcem "scim". Volitelně znovu použít staré mapování atributů spuštěním níže uvedeného příkazu, nahrazením [new-job-id] s novým ID úlohy, které jste právě zkopírovali, a zadáním výstupu JSON z kroku #7 jako tělo požadavku.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. Vraťte se do prvního okna webového prohlížeče a vyberte kartu **Zřizování** pro vaši aplikaci.
13. Ověřte konfiguraci a spusťte úlohu zřizování. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Můžu přidat novou aplikaci mimo galerii, která má staré chování zřizování uživatelů?

Ano. Pokud jste zakódovali aplikaci na staré chování, které existovalo před opravami, a potřebujete nasadit novou instanci, postupujte podle následujícího postupu. Tento postup popisuje, jak pomocí rozhraní Microsoft Graph API a průzkumníka rozhraní MICROSOFT Graph API vytvořit úlohu zřizování SCIM, která vykazuje staré chování.
 
1. Přihlaste se https://portal.azure.comk portálu Azure na adrese .
2. V **části Azure Active Directory > Podnikové aplikace > Vytvořit část aplikace** na webu Azure Portal vytvořte novou aplikaci mimo **galerii.**
3. V části **Vlastnosti** nové vlastní aplikace zkopírujte **ID objektu**.
4. V novém okně webového https://developer.microsoft.com/graph/graph-explorer prohlížeče přejděte na a přihlaste se jako správce pro klienta Azure AD, kde se vaše aplikace přidá.
5. V Průzkumníku grafů spusťte následující příkaz a inicializujte konfiguraci zřizování vaší aplikace.
   Nahraďte "[id objektu]" ID instancí (ID objektu) zkopírované ze třetího kroku.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. Vraťte se do prvního okna webového prohlížeče a vyberte kartu **Zřizování** pro vaši aplikaci.
7. Dokončete konfiguraci zřizování uživatelů obvyklým způsobem.


## <a name="next-steps"></a>Další kroky
[Další informace o zřizování a zrušení zřizování aplikacíM SaaS](user-provisioning.md)

