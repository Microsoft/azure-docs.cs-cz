---
title: Pomocí SCIM 2.0 protokol dodržování předpisů služby zřizování uživatelů služby Azure AD – známé problémy a jejich řešení | Dokumentace Microsoftu
description: Jak řešit běžné problémy s kompatibilitou protokol čelí při přidávání aplikace mimo galerii, která podporuje SCIM 2.0 do služby Azure AD
services: active-directory
documentationcenter: ''
author: asmalser
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asmalser
ms.openlocfilehash: 6aa4be204070423f91a2a4494b89a0bb8c8f5926
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258711"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Známé problémy a řešení pomocí SCIM 2.0 protokol dodržování předpisů služby zřizování uživatelů služby Azure AD

Azure Active Directory (Azure AD) je možné automaticky zřizovat uživatele a skupiny pro všechny aplikace nebo systému, který je přední stěnou pomocí webové služby s využitím rozhraní definované v [systému pro protokol mezi doménami Identity Management (SCIM) 2.0 specifikace](https://tools.ietf.org/html/draft-ietf-scim-api-19). 

Podpora služby Azure AD pro protokol SCIM 2.0 je popsaný v [pomocí systému pro mezi doménami Identity Management (SCIM) k automatickému zřízení uživatelů a skupin ze služby Azure Active Directory do aplikací](use-scim-to-provision-users-and-groups.md), se seznamem konkrétní části protokol, který implementuje za účelem automatické zřizování uživatelů a skupin ze služby Azure AD a aplikace s podporou SCIM 2.0.

Tento článek popisuje aktuálního i staršího problémy s zřizování služby dodržování protokol SCIM 2.0 a jak tyto problémy obejít uživatelů Azure AD.

> [!IMPORTANT]
> Nejnovější aktualizace pro klienta služby Azure AD uživatele pro zřizování SCIM služby byl proveden na 18. prosince 2018. Tato aktualizace řeší problémy s kompatibilitou uvedené v následující tabulce. Přečtěte si nejčastější dotazy níže pro další informace o této aktualizaci.

## <a name="scim-20-compliance-issues-and-status"></a>Problémy s dodržováním předpisů SCIM 2.0 a stav

| **Problém dodržování předpisů SCIM 2.0** |  **Oprava?** | **Opravte datum**  |  
|---|---|---|
| Azure AD vyžaduje "nebo scim" v kořenovém adresáři aplikace je adresa URL koncového bodu SCIM  | Ano  |  18. prosince 2018 | 
| Atributy rozšíření použít tečku "."zápis před názvy atributů místo dvojtečka":" zápis |  Ano  | 18. prosince 2018  | 
|  Oprava žádosti více hodnotami atributů obsahovat řiďte se syntaxí filtru neplatná cesta | Ano  |  18. prosince 2018  | 
|  Požadavky na vytvoření skupiny obsahují neplatné schéma identifikátoru URI | Ano  |  18. prosince 2018  |  

## <a name="were-the-services-fixes-described-automatically-applied-to-my-pre-existing-scim-app"></a>Jsou opravy služeb popsány, automaticky využije na mé existující aplikace SCIM?

Ne. Bude mít k rozbíjející změny SCIM aplikace, které bylo kódováno k práci s starší chování, změny nebyly automaticky použít pro existující aplikace.

Změny se použijí na všechny nové [aplikace mimo Galerii SCIM](configure-single-sign-on-non-gallery-applications.md) nakonfigurované na webu Azure Portal, po datu opravy.

Informace o tom, jak migrovat existující Úloha zřizování uživatele zahrnout nejnovější opravy najdete v další části.

## <a name="can-i-migrate-an-existing-scim-based-user-provisioning-job-to-include-the-latest-service-fixes"></a>Můžete migrovat stávající na základě SCIM zřizování uživatelů úlohy zahrnout nejnovější opravy služby?

Ano. Pokud už používáte tato instance aplikace pro jednotné přihlašování a potřebujete migrovat existující Úloha zřizování zahrnout nejnovější opravy, postupujte podle níže uvedeného postupu. Tento postup popisuje, jak používat rozhraní Microsoft Graph API a Průzkumníka Microsoft Graph API k odebrání existující aplikaci SCIM vaše staré úlohy zřizování a vytvořte novou vykazuje nové chování.

> [!NOTE]
> Pokud vaše aplikace je stále ve vývoji a nebyla ještě nasazena pro jednotné přihlašování nebo zřizování uživatelů, je odstranit položku aplikace v Nejjednodušším řešením **Azure Active Directory > podnikové aplikace**tématu na webu Azure portal a jednoduše přidat novou položku pro aplikace pomocí **vytvoření aplikace > mimo galerii** možnost. Jedná se o alternativu ke spuštění níže uvedeného postupu.
 
1. Přihlaste se k webu Azure portal na https://portal.azure.com.
2. V **Azure Active Directory > podnikové aplikace** části na webu Azure Portal, vyhledejte a vyberte svou stávající aplikaci SCIM.
3.  V **vlastnosti** část vaší existující aplikace SCIM kopírování **ID objektu**.
4.  V novém okně webového prohlížeče, přejděte na https://developer.microsoft.com/en-us/graph/graph-explorer a přihlaste se jako správce pro tenanta Azure AD, kde se přidá vaši aplikaci.
5. V Graph Exploreru spusťte následující příkaz k vyhledání ID zřizování úlohy. Nahraďte ID objektu zabezpečení (ID objektu) zkopírovanými z třetí krok služba "[object-id]".
 
 `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

 ![Získat úlohy](./media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "úloh") 


6. Ve výsledcích zkopírujte úplný řetězec "ID", který začíná "customappsso" nebo "scim".
7. Spusťte následující příkaz pro načtení konfigurace mapování atributů, takže můžete vytvořit zálohu. Použít stejné [object-id] jako před a nahraďte [id úlohy] zřizování ID úlohy, které jsou zkopírovány z posledního kroku.
 
 `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
 ![Získat schéma](./media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "získat schéma") 

8. Kopírovat výstup JSON v posledním kroku a uložte ho do textového souboru. Tato položka obsahuje všechny vlastní – mapování atributů přidá do vaší aplikace starý a by měla být přibližně několik tisíc řádků JSON.
9. Spuštěním následujícího příkazu odstraňte úlohu zřizování:
 
 `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. Spusťte následující příkaz k vytvoření nové úlohy zřizování, který má nejnovější opravy služby.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs `
 `{   templateId: "scim"   } `
   
11. Ve výsledcích poslední krok zkopírujte úplný řetězec "ID", který začíná "scim". Znovu použijte v případě potřeby vaše staré mapování atributů spuštěním příkazu nahrazujte [nové-– id úlohy] s novou úlohu s ID jste zkopírovali a zadat, které ve formátu JSON výstup z kroku 7 # jako datovou část požadavku.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema `
 `{   <your-schema-json-here>   }`

12. Vraťte se do první okno webového prohlížeče a vyberte **zřizování** kartu pro vaši aplikaci.
13. Zkontrolujte konfiguraci a spusťte úlohy zřizování. 

## <a name="can-i-add-a-new-non-gallery-app-that-has-the-old-user-provisioning-behavior"></a>Můžete přidat novou aplikaci mimo galerii, která má starý zřizování chování uživatelů?

Ano. Pokud měl programového aplikace do staré chování, které existovaly před opravy a budete muset nasadit novou instanci, postupujte podle níže uvedeného postupu. Tento postup popisuje, jak použít rozhraní Microsoft Graph API a Průzkumník Microsoft Graph API k vytvoření úlohy zřizování SCIM, vykazující staré chování.
 
1.  Přihlaste se k webu Azure portal na https://portal.azure.com.
2. v **Azure Active Directory > podnikové aplikace > Vytvoření aplikace** části na webu Azure Portal vytvořte novou **mimo galerii** aplikace.
3.  V **vlastnosti** části nové vlastní aplikace, kopie **ID objektu**.
4.  V novém okně webového prohlížeče, přejděte na https://developer.microsoft.com/en-us/graph/graph-explorer a přihlaste se jako správce pro tenanta Azure AD, kde se přidá vaši aplikaci.
5. V Graph Exploreru spusťte následující příkaz k inicializaci konfigurace zřizování pro vaši aplikaci.
Nahraďte ID objektu zabezpečení (ID objektu) zkopírovanými z třetí krok služba "[object-id]".

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "customappsso"   }`
 
6. Vraťte se do první okno webového prohlížeče a vyberte **zřizování** kartu pro vaši aplikaci.
7. Dokončení zřizování konfigurace, jako obvykle uživatelů.


## <a name="next-steps"></a>Další postup
[Další informace o zřizování a zrušení zřizování pro aplikace SaaS](user-provisioning.md)

