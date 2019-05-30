---
title: Pokyny pro vydavatele Azure Marketplace a AppSource | Azure
description: Pokyny pro Azure Marketplace a AppSource pro vydavatele aplikace a služby
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 57eeb9b6ac6eb826e2b371e6ff4dfd71f8cef49c
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258981"
---
# <a name="guidelines"></a>Pokyny  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Pokyny pro Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Pokyny k tvorbě ID Microsoftu ke správě účtu marketplace  
Pokud více než jedna osoba vyžaduje přístup ke stejným ID Microsoft používá k vytvoření účtu marketplace, by měl důsledném následování těchto směrnic vám pomůže vytvořit účet společnosti. 

>[!IMPORTANT]
>Povolit více uživatelů pro přístup k účtu Microsoft Developer Center (Centrum pro vývojáře), společnost Microsoft doporučuje použít Azure Active Directory (Azure AD) pro přiřazení rolí pro jednotlivé uživatele. Každý uživatel musí přístup k účtu, když se přihlásíte pomocí jednotlivých přihlašovacích údajů Azure AD. Vytvoření účtu Microsoft ID pomocí e-mailovou adresu v doméně registrované pro vaši společnost Microsoft navrhuje, že e-mailu nebudou přiřazené k jednotlivec. Příklad: `windowsapps@fabrikam.com`.  
>*   Další informace najdete [problému: ID Microsoftu v Azure AD Federovaná doména](#issue-microsoft-id-in-an-azure-ad-federated-domain) oddílu.  

*   Omezit přístup k Microsoft ID na nejmenší možný počet vývojářů. 
*   Nastavení firemního e-mailu distribučního seznamu (DL), která obsahuje všechny uživatele, kteří musí přístup k vašemu účtu Dev Center. Přidáte e-mailová adresa bude používat pro informace o zabezpečení. Umožňuje distribučního seznamu všech zaměstnanců na seznamu získat zabezpečovací kódy na požádání a spravovat informace o zabezpečení Microsoft ID. Nastavení distribučního seznamu není proveditelné, vlastník jednotlivé e-mailový účet musí být k dispozici pro přístup k a sdílet kód zabezpečení po zobrazení výzvy.  
    *   Vlastník například se zobrazí výzva při přidání nové informace o zabezpečení pro Microsoft ID nebo Microsoft ID přistupuje z nového zařízení.  
*   Přidáte telefonní číslo společnosti, který nevyžaduje, aby rozšíření a je přístupné členům týmu klíče.  
*   Obecně platí je potřeba vývojářům používat důvěryhodná zařízení pro přihlášení na svém účtu centra vývojářů. Všichni členové týmu klíče by měl mít přístup k důvěryhodným zařízením. Používat důvěryhodná zařízení pro přístup k sníží se požadavky na odeslání zabezpečovací kódy, když uživatel přistupuje k účtu centra vývojářů.  
*   V případě potřeby udělit přístup k účtu centra vývojářů z nedůvěryhodné počítače by měly omezit přístup k více než pět vývojářů. V ideálním případě by vývojáři měli přístup k účtu z počítačů, které sdílejí stejné zeměpisné a síťové umístění.  
*   Často zkontrolujte a ověřte informace o zabezpečení.  
    *   Chcete-li zobrazit informace o zabezpečení, navštivte web Security stránka nastavení umístění [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Váš účet vývojáře pro by měla primárně přistupovat z důvěryhodné počítače. Je důležité získat přístup z důvěryhodné počítače, protože je limit pro počet kódů generovaných každý účtu centra vývojářů pro za týden. Použití důvěryhodné počítače také umožní nejbezpečnější a konzistentní přihlašování. 
*   Další informace o další pokyny účtu Dev Center a zabezpečení, navštivte počáteční stránka účtu vývojáře umístěného v [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problém: Microsoft ID do federované domény Azure AD  
Podnikový účet může Federovaná pomocí služby Azure Active Directory (Azure AD). Pokud se pokusíte vytvořit Microsoft ID, pomocí podnikové e-mailovou adresu, která je Federovaná pomocí služby Azure AD, obdržíte chybu. Pokud obdržíte chybu, pak byste měli zkontrolovat s pracovníky IT, abychom potvrdili, že váš účet je Federovaná pomocí služby Azure AD. Microsoft pracuje na řešení se Azure AD Federovaná e-mailu je známý problém.  
*   Další informace o službě Azure AD, najdete na webu Azure Active Directory dokumentaci stránky umístění [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Společnost Microsoft doporučuje alternativní řešení. Postupujte podle těchto kroků a vytvořte novou e-mailovou adresu v `outlook.com` domény a vytvořte pravidlo pro předávání komunikace.  
1.  Přejděte na stránku vytvoření účtu a klikněte na získat nový odkaz e-mailovou adresu. 
    *   K registraci pro Microsoft ID, navštivte stránka pro vytvoření účtu na [signup.live.com/signup](https://signup.live.com/signup).  
2.  Vytvořit novou e-mailovou adresu a zadejte heslo. Nové ID společnosti Microsoft a e-mailové schránky v `outlook.com` doména je vytvořená. V registraci pokračujte, dokud je účet vytvořený.  

    >[!IMPORTANT]
    >Je nutné použít e-mailovou adresu nebo distribuční seznam, který je registrovaný jako ID Microsoftu k registraci ve službě Dev Center. Společnost Microsoft doporučuje používat distribuční seznam odebrat závislost jednotlivců. Pokud vaše e-mailovou adresu nebo v distribučním seznamu není zaregistrovaný, pak budete muset zaregistrovat nyní.    

    >[!Important]
    >Pokud vaše všechny e-mailová adresa je umístěn v `Microsoft` firemní domény, pak nemůžete použít k registraci ve službě Dev Center.  

3.  Po vytvoření Microsoft ID s e-mailovou adresu Outlook přihlášení do vaší poštovní schránce aplikace Outlook. Vytvořte pravidlo přeposílání e-mailu. Pravidlo přeposílání e-mailu by se měla přesunout všechny e-mailů, které nejsou přijímána v poštovní schránce aplikace Outlook pro e-mailovou adresu ve vaší doméně, kterou jste vytvořili ke správě vašeho účtu na webu marketplace.  
    *   Pro přihlášení do vaší poštovní schránce aplikace Outlook, najdete na stránce aplikace Outlook na [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Další informace o předávání pravidla, najdete pomocí pravidel v Outlook Web App automaticky předávat zprávy na jinou stránku účtu v [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Pravidlo pro předávání odešle všechny e-mailu a komunikace dostali e-mailový účet Outlooku e-mailovou adresu v doméně registrované pro vaši společnost. Vaše `outlook.com` e-mailovou adresu musíte použít k ověřování v centru vývojářů pro i portál partnerů cloudu.  

## <a name="next-steps"></a>Další postup

*   Přejděte [Azure Marketplace a AppSource příručce pro vydavatele](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) stránky. 
 
---
