---
title: Pokyny pro Azure Marketplace a vydavatelappsource | Azure
description: Pokyny pro Azure Marketplace a AppSource pro vydavatele aplikací a služeb
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387597"
---
# <a name="guidelines"></a>Pokyny  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Pokyny pro Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Pokyny pro vytvoření Microsoft ID pro správu účtu marketplace  
Pokud více než jedna osoba vyžaduje přístup ke stejnému účtu Microsoft ID, které bylo použito k vytvoření účtu marketplace, měli byste postupovat podle těchto pokynů, které vám pomohou vytvořit firemní účet. 

>[!IMPORTANT]
>Chcete-li autorizovat přístup k účtu Microsoft Developer Center (Dev Center), společnost Microsoft doporučuje používat Azure Active Directory (Azure AD) k přiřazení rolí jednotlivým uživatelům. Každý uživatel musí přistupovat k účtu po přihlášení pomocí jednotlivých přihlašovacích údajů Azure AD. Vytvořte si Microsoft ID pomocí e-mailové adresy v doméně registrované ve vaší společnosti Microsoft navrhuje, aby e-mail nebyl přiřazen k jednotlivci. Příklad: `windowsapps@fabrikam.com`.  
>*   Další informace najdete v [tématu Problém: Microsoft ID v části federované domény Azure AD.](#issue-microsoft-id-in-an-azure-ad-federated-domain)  

*   Omezte přístup k Microsoft ID na nejmenší možný počet vývojářů. 
*   Nastavte firemní seznam distribuce e-mailů (DL), který zahrnuje všechny uživatele, kteří musí přistupovat k vašemu účtu Dev Center. Přidejte e-mailovou adresu DL do svých bezpečnostních údajů. Dl umožňuje všem zaměstnancům v seznamu přijímat bezpečnostní kódy na požádání a spravovat informace o zabezpečení pro vaše Microsoft ID. Pokud nastavení distribučního seznamu není možné, musí být vlastník jednotlivého e-mailového účtu k dispozici pro přístup a sdílení bezpečnostního kódu po zobrazení výzvy.  
    *   Vlastník je například vyzván, když jsou do Microsoft ID přidány nové informace o zabezpečení nebo když je k Microsoft ID přistupováno z nového zařízení.  
*   Přidejte telefonní číslo společnosti, které nevyžaduje rozšíření a je přístupné klíčovým členům týmu.  
*   Obecně byste měli vyžadovat, aby vývojáři používali důvěryhodná zařízení k přihlášení k účtu Dev Center. Všichni klíčoví členové týmu by měli mít přístup k důvěryhodným zařízením. Používání důvěryhodných zařízení pro přístup snižuje požadavky na odesílání bezpečnostních kódů, když někdo přistupuje k účtu Dev Center.  
*   Pokud jste povinni udělit přístup k účtu Dev Center z nedůvěryhodného počítače, měli byste omezit přístup na maximálně pět vývojářů. V ideálním případě by vývojáři měli přistupovat k účtu z počítačů, které sdílejí stejné zeměpisné a síťové umístění.  
*   Často kontrolujte a ověřujte své bezpečnostní údaje.  
    *   Informace o zabezpečení naleznete na stránce Nastavení zabezpečení umístěné na [adrese account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

K účtu Dev Center by měl být primárně přístupný z důvěryhodných počítačů. Je velmi důležité, abyste přistupovali z důvěryhodných počítačů, protože počet kódů generovaných na účet Centra pro dev center za týden je omezen. Používání důvěryhodných počítačů také umožňuje nejbezpečnější a nejkonzistentnější přihlašování. 
*   Další informace o dalších pokynech k účtu Dev Center a zabezpečení naleznete na stránce Otevření vývojářského účtu umístěné na [adrese docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problém: Microsoft ID ve federované doméně Azure AD  
Váš firemní účet může být federován prostřednictvím Služby Azure Active Directory (Azure AD). Pokud se pokusíte vytvořit Microsoft ID pomocí podnikové e-mailové adresy, která je federována s Azure AD, pak se zobrazí chyba. Pokud se zobrazí chyba, měli byste se poradit se svým IT týmem a potvrdit, že váš účet je federován prostřednictvím Azure AD. Federovaný e-mail Azure AD je známý problém a Microsoft pracuje na jeho vyřešení.  
*   Další informace o azure ad, navštivte azure active directory dokumentace stránku umístěná na [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Společnost Microsoft doporučuje řešení. Následujícím postupem vytvořte novou e-mailovou adresu v `outlook.com` doméně a vytvořte pravidlo pro předávání komunikace.  
1.  Přejděte na stránku Vytvořit účet a klikněte na odkaz Získat novou e-mailovou adresu. 
    *   Pokud se chcete zaregistrovat k účtu Microsoft ID, navštivte stránku Vytvořit účet, která se nachází na [signup.live.com/signup](https://signup.live.com/signup).  
2.  Vytvořte novou e-mailovou adresu a zadejte heslo. Vytvoří se nové Microsoft ID a `outlook.com` e-mailová schránka v doméně. Pokračujte v procesu registrace, dokud nebude účet vytvořen.  

    >[!IMPORTANT]
    >Chcete-li se zaregistrovat v centru pro ochranu před technologiemi, musíte použít e-mailovou adresu nebo distribuční seznam, který je registrován jako Microsoft ID. Společnost Microsoft doporučuje použít distribuční seznam k odebrání závislosti od jednotlivců. Pokud vaše e-mailová adresa nebo distribuční seznam není registrován, musíte se nyní zaregistrovat.    

    >[!Important]
    >Pokud se vaše e-mailová adresa nachází v doméně `Microsoft` společnosti, nemůžete ji použít k registraci v Centru pro firmy.  

3.  Po vytvoření Microsoft ID s e-mailovou adresou Outlooku se přihlaste do poštovní schránky Outlooku. Vytvořte pravidlo pro předávání e-mailů. Pravidlo pro předávání e-mailů by mělo přesunout všechny e-maily přijaté v poštovní schránce aplikace Outlook na e-mailovou adresu ve vaší doméně, kterou jste vytvořili pro správu účtu marketplace.  
    *   Pokud se chcete přihlásit do poštovní schránky Outlooku, navštivte stránku Outlooku umístěnou na [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Další informace o pravidlech předávání najdete v článku Použití pravidel v Outlook Web Appu k automatickému předávání zpráv na jinou stránku účtu umístěnou na [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Pravidlo předávání odesílá všechny e-maily a komunikace přijaté v e-mailovém účtu aplikace Outlook na e-mailovou adresu v doméně registrované ve vaší společnosti. Vaše `outlook.com` e-mailová adresa musí být použita k ověření na portálu Dev Center i Cloud Partner Portal.  

## <a name="next-steps"></a>Další kroky

*   Navštivte stránku [Azure Marketplace a Průvodce vydavatelem AppSource.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) 
 
---
