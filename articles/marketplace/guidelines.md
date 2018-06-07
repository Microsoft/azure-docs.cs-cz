---
title: Pokyny pro Azure Marketplace a AppSource vydavatele | Azure
description: Pokyny pro Azure Marketplace a AppSource pro vydavatele aplikace a služby
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/05/2018
ms.author: ellacroi
ms.openlocfilehash: 135f934cd6b352dad9e4cea5a14406804f31b66b
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825081"
---
# <a name="guidelines"></a>Pokyny  

<!--
## Guidelines for AppSource  
-->
---  

## <a name="guidelines-for-azure-marketplace"></a>Pokyny pro Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Pokyny pro vytváření a ID Microsoft ke správě účtu webu marketplace  
Pokud víc než jedna osoba vyžaduje přístup ke stejným ID Microsoft používá k vytvoření účtu webu marketplace, pak postupujte podle těchto pokynů, které vám pomůžou vytvořit účet společnosti. 

>[!IMPORTANT]
>K autorizaci několika uživatelům přístup k účtu Microsoft Developer Center (Dev Center), Microsoft doporučuje, že používáte Azure Active Directory (Azure AD) k přiřazení rolí pro jednotlivé uživatele. Každý uživatel musí přístup k účtu po přihlášení pomocí individuální přihlašovací údaje Azure AD. Vytvoření ID služeb Microsoft pomocí e-mailovou adresu v doméně zaregistrován pro vaši společnost Microsoft naznačuje, že e-mailu, nelze ho přiřadit až k jednotlivcům. Příklad: `windowsapps@fabrikam.com`.  
>*   Další informace najdete v článku [problém: ID společnosti Microsoft ve službě Azure AD Federovaná domény](#issue:-microsoft-id-in-an-azure-ad-federated-domain) části.  

*   Omezit přístup k Microsoft ID, který má nejmenší možný počet vývojáři. 
*   Nastavení podnikových e-mailů distribučního seznamu (DL), který zahrnuje všechny uživatele, kteří musí přístup k vašemu účtu Dev Center. E-mailovou adresu DL přidáte do vašich bezpečnostních údajů. DL umožňuje všichni zaměstnanci v seznamu můžete získat zabezpečovací kódy vyžádání a spravovat informace o zabezpečení Microsoft ID. Nastavení distribučního seznamu není vhodná, vlastník jednotlivé e-mailového účtu musí být k dispozici pro přístup k a sdílet bezpečnostní kód po zobrazení výzvy.  
    *   Například vlastník vyzván při přidání nové informace o zabezpečení Microsoft ID nebo ID společnosti Microsoft, se získají z nového zařízení.  
*   Přidejte telefonní číslo společnosti, který nevyžaduje rozšíření a je přístupný pro členy týmu klíče.  
*   Obecně byste měli vyžadovat vývojářům používat důvěryhodné zařízení pro přihlášení do účtu Dev Center. Všichni členové týmu klíče mají mít přístup k důvěryhodná zařízení. Používá pro přístup k důvěryhodné zařízení, sníží se požadavky na odeslání zabezpečovací kódy, když někdo přistupuje k účtu Dev Center.  
*   Pokud jsou potřeba k udělení přístupu k účtu Dev Center z nedůvěryhodné počítače, měli byste omezit přístup k více než pět vývojáři. V ideálním případě by vaše vývojáři měli přístup k účtu z počítače, které sdílejí stejné zeměpisné a síťové umístění.  
*   Často zkontrolujte a ověřte své informace o zabezpečení.  
    *   Pokud chcete zobrazit informace o zabezpečení, navštivte web Security stránka nastavení nacházející se v [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Účtu Dev Center by měly být dostupné především z důvěryhodné počítače. Je důležité přístup k z důvěryhodné počítače, protože existuje omezení počtu kódů generovaných každý účet Centrum vývojářů pro za týden. Použití důvěryhodné počítače také umožňuje nejbezpečnější a konzistentní přihlašování. 
*   Další informace o další pokyny účtu Dev Center a zabezpečení, najdete v článku otevírání nacházející se stránku účtu vývojáře v [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---  

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problém: Microsoft ID v federované domény Azure AD  
Podnikový účet může být federovaný pomocí služby Azure Active Directory (Azure AD). Pokud se pokusíte vytvořit ID Microsoft podnikových e-mailovou adresu, která je Federovaná pomocí Azure AD, obdržíte chybu. Pokud se zobrazí chyba, pak je potřeba zkontrolovat u vašeho IT týmu a ověřte, zda že váš účet federovaný pomocí služby Azure AD. Azure AD Federovaná e-mailu se o známý problém a společnost Microsoft pracuje na jeho řešení.  
*   Další informace o Azure AD, najdete v Azure Active Directory dokumentaci stránky nacházející se v [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Společnost Microsoft doporučuje alternativní řešení. Postupujte podle těchto kroků můžete vytvořit novou e-mailovou adresu v `outlook.com` domény a vytvořte pravidlo pro předávání vaší komunikace.  
1.  Přejděte na stránku vytvořit účet a klikněte na Get nový odkaz e-mailovou adresu. 
    *   Pro ID služeb Microsoft, přejděte na stránku vytvořit účet, které se nachází na [signup.live.com/signup](https://signup.live.com/signup).  
2.  Vytvořte novou e-mailovou adresu a zadejte heslo. Nové ID společnosti Microsoft a e-mailové schránky v `outlook.com` vytvoření domény. Proces registrace pokračujte, dokud je účet vytvořený.  

    >[!IMPORTANT]
    >Je nutné použít e e-mailovou adresu nebo distribuční seznam, který je zaregistrován jako ID Microsoft zaregistrovat na webu Dev Center. Microsoft doporučuje používat distribuční seznam odebrání závislostí jednotlivce. Pokud není registrované e-mailovou adresu nebo distribuční seznam, pak je nutné zaregistrovat nyní.    

    >[!Important]
    >Pokud vaše všechny e-mailová adresa je umístěná v `Microsoft` firemní domény, pak nemůžete použít pro registraci na Dev Center.  

3.  Po vytvoření Microsoft ID Outlook e-mailové adrese, přihlaste se k vaší poštovní schránce Outlooku. Vytvořte pravidlo předávání e-mailu. E-mailu předávání pravidlo přesuňte všechny e-mailů, které jsou přijaty v poštovní schránce Outlooku e-mailovou adresu ve vaší doméně, kterou jste vytvořili pro správu účtu webu marketplace.  
    *   Přihlaste se k vaší poštovní schránce Outlooku, najdete na stránce Outlook nacházející se v [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Další informace o předávání pravidla, najdete v článku použití pravidel v aplikaci Outlook Web App pro automatické předávání zpráv na jiné stránky účtu nacházející se v [support.office.com/article/ Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Pravidlo předávání odešle všechny e-mailu a komunikace obdrželi v e-mailový účet Outlook e-mailovou adresu v doméně zaregistrován pro vaši společnost. Vaše `outlook.com` e-mailovou adresu musíte použít k ověřování v centru vývojářů a portál pro partnery cloudu.  

## <a name="next-steps"></a>Další postup
*   Přejděte [AppSource vydavatele průvodce a Azure Marketplace](./marketplace-publishers-guide.md) stránky.  
 
---  
