---
title: Pokyny pro Azure Marketplace a AppSource vydavatele | Azure
description: Pokyny pro Azure Marketplace a AppSource pro vydavatele aplikací a služeb
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 371f36e13c244439a583cbeb7ff06c6a1283d272
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825936"
---
# <a name="guidelines"></a>Pokyny  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Pokyny pro Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Pokyny pro vytvoření ID Microsoftu pro správu účtu Marketplace  
Pokud více než jedna osoba potřebuje přístup ke stejnému ID Microsoftu, které jste použili k vytvoření účtu Marketplace, měli byste postupovat podle těchto pokynů, které vám pomůžou vytvořit účet společnosti. 

>[!IMPORTANT]
>Aby bylo možné udělit přístup k účtu Microsoft Developer Center (Dev Center) více uživatelům, společnost Microsoft doporučuje použít službu Azure Active Directory (Azure AD) k přiřazení rolí jednotlivým uživatelům. Každý uživatel musí mít přístup k účtu, když se přihlásí pomocí individuálních přihlašovacích údajů služby Azure AD. Vytvořte své ID Microsoftu pomocí e-mailové adresy v doméně registrované pro vaši společnost Microsoft navrhne, aby se e-mail nepřiřadil k jednotlivcům. Příklad: `windowsapps@fabrikam.com`.  
>*   Další informace najdete v části [problém: Microsoft ID v federované doméně Azure AD](#issue-microsoft-id-in-an-azure-ad-federated-domain) .  

*   Omezte přístup k Microsoft ID na nejmenší možný počet vývojářů. 
*   Nastavte podnikový distribuční seznam (DL), který zahrnuje všechny uživatele, kteří musí mít přístup k vašemu účtu centra vývojářů. Přidejte e-mailovou adresu DL do informací o zabezpečení. Poštovní seznam DL umožňuje všem zaměstnancům v seznamu přijímat kódy zabezpečení, pokud jsou požadovány, a spravovat informace o zabezpečení pro vaše ID Microsoftu. Pokud nastavení distribučního seznamu není proveditelné, musí mít vlastník jednotlivého e-mailového účtu přístup a po zobrazení výzvy bude sdílet bezpečnostní kód.  
    *   Vlastník je například vyzván, když se do ID Microsoftu přidá nové informace o zabezpečení nebo když se k ID Microsoftu přistupuje z nového zařízení.  
*   Přidejte telefonní číslo společnosti, které nevyžaduje rozšíření a je přístupné pro klíčové členy týmu.  
*   Obecně byste měli vyžadovat, aby se vývojáři k účtu služby Dev Center přihlásili pomocí důvěryhodných zařízení. Všichni členové každého klíčového týmu by měli mít přístup k důvěryhodným zařízením. Použití důvěryhodných zařízení pro přístup omezuje požadavek na odeslání kódů zabezpečení, když někdo přistupuje k účtu centra vývojářů.  
*   Pokud potřebujete udělit přístup k účtu centra vývojářů z nedůvěryhodného počítače, měli byste omezit přístup maximálně na pět vývojářů. V ideálním případě by měli vývojáři k účtu přistupovat z počítačů, které sdílejí stejné geografické umístění a umístění v síti.  
*   Často zkontrolujte a ověřte informace o zabezpečení.  
    *   Chcete-li zobrazit informace o zabezpečení, navštivte stránku nastavení zabezpečení, která se nachází na adrese [account.Live.com/Proofs/Manage](https://account.live.com/proofs/Manage).

K vašemu účtu služby Dev Center by měl být z důvěryhodných počítačů primárně k dispozici. Je velmi důležité, abyste měli přístup z důvěryhodných počítačů, protože počet kódů generovaných v jednom z účtů služby Dev Center za týden je omezený. Použití důvěryhodných počítačů také umožňuje nejbezpečnější a konzistentní přihlašovací prostředí. 
*   Další informace o dalších pokynech a zabezpečení účtů centra pro vývoj najdete na stránce s informacemi o otevření vývojářského účtu, který se nachází na adrese [docs.Microsoft.com/Windows/UWP/Publish/Opening-a-Developer-Account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problém: ID Microsoftu v federované doméně Azure AD  
Váš podnikový účet může být federovaný prostřednictvím Azure Active Directory (Azure AD). Pokud se pokusíte vytvořit ID Microsoftu pomocí podnikové e-mailové adresy, která je federované s Azure AD, zobrazí se chybová zpráva. Pokud se zobrazí chyba, měli byste se podívat na váš IT tým a ověřit, zda je váš účet federovaný prostřednictvím služby Azure AD. Federovaný e-mail Azure AD je známý problém a Microsoft pracuje na jejich řešení.  
*   Další informace o službě Azure AD najdete na stránce dokumentace Azure Active Directory na adrese [docs.Microsoft.com/Azure/Active-Directory](https://docs.microsoft.com/azure/active-directory).

Microsoft doporučuje alternativní řešení. Pomocí těchto kroků můžete vytvořit novou e-mailovou adresu v `outlook.com` doméně a vytvořit pravidlo pro předání komunikace.  
1.  Přejděte na stránku vytvořit účet a klikněte na odkaz získat novou e-mailovou adresu. 
    *   Pokud se chcete zaregistrovat ke svému ID Microsoftu, navštivte stránku vytvořit účet na adrese [signup.Live.com/signup](https://signup.live.com/signup).  
2.  Vytvořte novou e-mailovou adresu a zadejte heslo. Vytvoří se nové ID společnosti Microsoft a poštovní schránka v doméně `outlook.com`. Pokračujte v procesu registrace až do chvíle, kdy se účet vytvoří.  

    >[!IMPORTANT]
    >Je nutné použít e-mailovou adresu nebo distribuční seznam, který je zaregistrován jako ID Microsoftu k registraci na webu Dev Center. Microsoft doporučuje, abyste k odebrání závislostí od jednotlivců použili distribuční seznam. Pokud vaše e-mailová adresa nebo distribuční seznam není zaregistrované, musíte se zaregistrovat hned.    

    >[!Important]
    >Pokud se vaše e-mailová adresa nachází v `Microsoft` doméně společnosti, nebudete ji moct používat k registraci ve vývojářském centru.  

3.  Po vytvoření ID Microsoftu s e-mailovou adresou Outlooku se přihlaste do poštovní schránky Outlooku. Vytvořte pravidlo pro předávání e-mailu. Pravidlo přeposlání e-mailu by mělo přesunout všechny e-maily, které jsou přijaty v poštovní schránce aplikace Outlook, na e-mailovou adresu ve vaší doméně, kterou jste vytvořili pro správu účtu  
    *   Pokud se chcete přihlásit k poštovní schránce Outlooku, navštivte stránku Outlooku, která se nachází na adrese [Outlook.Live.com/OWA](https://outlook.live.com/owa).  
    *   Další informace o pravidlech předávání najdete v části použití pravidel v Outlook Web App k automatickému předávání zpráv na jinou stránku účtu, která se nachází na adrese [support.Office.com/article/use-Rules-in-Outlook-Web-App-to-automatically-Forward-Messages-to-Another-Account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Předávací pravidlo posílá všechny e-maily a komunikace přijaté na e-mailovém účtu Outlooku na e-mailovou adresu v doméně zaregistrovanou ve vaší společnosti. Vaše e-mailová adresa `outlook.com` se musí použít k ověřování na vývojářském centru i v portál partnerů cloudu.  

## <a name="next-steps"></a>Další kroky

*   Navštivte stránku [Příručka pro vydavatele Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) . 
 
---
