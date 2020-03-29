---
title: Povolení služby Enterprise State Roaming v Azure Active Directory
description: Nejčastější dotazy týkající se nastavení podnikového státního roamingu v zařízeních se systémem Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672396"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Povolení služby Enterprise State Roaming v Azure Active Directory

Enterprise State Roaming je k dispozici pro všechny organizace s licencí Azure AD Premium nebo Enterprise Mobility + Security (EMS). Další informace o tom, jak získat předplatné Azure AD, najdete na [stránce produktu Azure AD](https://azure.microsoft.com/services/active-directory).

Když povolíte roaming Enterprise State Roaming, vaší organizaci bude automaticky udělena bezplatná licence s omezeným použitím pro ochranu Azure Rights Management z Azure Information Protection. Toto bezplatné předplatné je omezeno na šifrování a dešifrování podnikových nastavení a dat aplikací synchronizovaných pomocí podnikového státního roamingu. Abyste mohli využívat všechny možnosti služby Azure Rights Management, musíte mít [placené předplatné.](https://azure.microsoft.com/pricing/details/information-protection/)

> [!NOTE]
> Tento článek se týká prohlížeče microsoft edge legacy založeného na HTML spuštěném v systému Windows 10 v červenci 2015. Tento článek se nevztahuje na nový prohlížeč založený na microsoft edge chromu vydaném 15. Další informace o chování synchronizace pro nový microsoft edge naleznete v článku [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Povolení podnikového státního roamingu

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com/).
1. Vyberte **možnost Azure Active Directory** &gt; **Devices** &gt; **Enterprise State Roaming**.
1. Vyberte **Uživatelé mohou synchronizovat nastavení a data aplikací mezi zařízeními**. Další informace naleznete v tématu [Konfigurace nastavení zařízení](/azure/active-directory/device-management-azure-portal).
  
   ![obrázek nastavení zařízení s popiskem Uživatelé mohou synchronizovat nastavení a data aplikací mezi zařízeními](./media/enterprise-state-roaming-enable/device-settings.png)
  
Aby zařízení s Windows 10 používalo službu Enterprise State Roaming, musí se zařízení ověřit pomocí identity Azure AD. Pro zařízení, která jsou připojena ke službě Azure AD, je primární přihlašovací identita uživatele jejich identitou Azure AD, takže není vyžadována žádná další konfigurace. U zařízení, která používají místní službu Active Directory, musí správce IT [konfigurovat hybridní zařízení Azure Active Directory připojená](hybrid-azuread-join-manual-steps.md). 

## <a name="data-storage"></a>Úložiště dat

Data enterprise state roamingu jsou hostovaná v jedné nebo více [oblastech Azure,](https://azure.microsoft.com/regions/) které nejlépe odpovídají hodnotě země nebo oblasti nastavené v instanci Služby Azure Active Directory. Data enterprise state roamingu jsou rozdělena na základě tří hlavních zeměpisných oblastí: Severní Amerika, EMEA a APAC. Data enterprise state roamingu pro klienta se místně nacházejí se zeměpisnou oblastí a nejsou replikována napříč oblastmi.  Například:

| Hodnota země/oblasti | mají své údaje hostované v |
| -------------------- | ------------------------ |
| Země/region EMEA, jako je Francie nebo Zambie | Jedna nebo více oblastí Azure v Evropě |
| Severoamerická země/oblast, například Spojené státy nebo Kanada | Jedna nebo více oblastí Azure v USA |
| Země/oblast APAC, například Austrálie nebo Nový Zéland | Jedna nebo více oblastí Azure v Asii |
| Jihoamerické a antarktické oblasti | Jedna nebo více oblastí Azure v USA |

Hodnota země nebo oblasti je nastavena jako součást procesu vytváření adresářů Azure AD a nelze ji následně upravit. Pokud potřebujete další podrobnosti o umístění úložiště dat, naložte lístek s [podporou Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Zobrazit stav synchronizace zařízení pro jednotlivé uživatele

Chcete-li zobrazit zprávu o stavu synchronizace zařízení pro jednotlivé uživatele, postupujte takto.

1. Přihlaste se do [Centra pro správu Azure AD](https://aad.portal.azure.com/).
1. Vyberte **možnost Uživatelé** &gt; **služby Azure Active Directory** &gt; **Všichni uživatelé**.
1. Vyberte uživatele a pak vyberte **Zařízení**.
1. V části **Zobrazit**vyberte **Nastavení synchronizace zařízení a data aplikací,** aby se zobrazil stav synchronizace.
  
   ![nastavení dat synchronizace zařízení](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Pokud jsou zařízení synchronizována pro tohoto uživatele, uvidíte zařízení, jak je znázorněno zde.
  
   ![Obrázek sloupcových dat synchronizace zařízení](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Uchovávání dat

Data synchronizovaná s cloudem Microsoftu pomocí podnikového státního roamingu se zachovávají, dokud nejsou ručně odstraněna nebo dokud není tato data určena jako zastaralá. 

### <a name="explicit-deletion"></a>Explicitní odstranění

Explicitní odstranění je, když správce Azure odstraní uživatele nebo adresáře nebo jinak požaduje explicitně, že data je třeba odstranit.

* **Odstranění uživatele**: Když je uživatel odstraněn ve službě Azure AD, data roamingu uživatelského účtu se odstraní po 90 až 180 dnech. 
* **Odstranění adresáře**: Odstranění celého adresáře ve službě Azure AD je okamžitá operace. Všechna data nastavení přidružená k tomuto adresáři budou odstraněna po 90 až 180 dnech. 
* **Na žádost odstranění**: Pokud správce Azure AD chce ručně odstranit data konkrétního uživatele nebo nastavení dat, správce můžete soubor lístek s [podporou Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Odstranění zastaralých dat

Data, ke kterým nikdo nebyl po dobu jednoho roku ("doba uchovávání") považována za zastaralá, a mohou být odstraněna z cloudu Microsoftu. Doba uchovávání se může změnit, ale nebude kratší než 90 dní. Zastaralá data mohou být specifickou sadou nastavení systému Windows/aplikace nebo všemi nastaveními pro uživatele. Například:

* Pokud žádná zařízení přístup k určité kolekci nastavení (například aplikace je odebrána ze zařízení, nebo skupina nastavení, jako je například "Téma" je zakázáno pro všechna zařízení uživatele), pak tato kolekce stane zastaralé po dobu uchování a mohou být odstraněny . 
* Pokud uživatel vypnul synchronizaci nastavení na všech svých zařízeních, nebude k dispozici žádný z dat nastavení a všechna data nastavení pro tohoto uživatele budou zastaralá a mohou být po uplynutí doby uchovávání odstraněna. 
* Pokud správce adresáře Azure AD vypne enterprise state roaming pro celý adresář, všichni uživatelé v tomto adresáři zastaví nastavení synchronizace a všechna data nastavení pro všechny uživatele se stanou zastaralými a mohou být odstraněna po uplynutí doby uchovávání informací. 

### <a name="deleted-data-recovery"></a>Odstranění obnovení dat

Zásady uchovávání dat nelze konfigurovat. Jakmile jsou data trvale odstraněna, nelze je obnovit. Data nastavení jsou však odstraněna pouze z cloudu společnosti Microsoft, nikoli ze zařízení koncového uživatele. Pokud se jakékoli zařízení později znovu připojí ke službě Enterprise State Roaming, nastavení se znovu synchronizuje a uloží do cloudu Microsoftu.

## <a name="next-steps"></a>Další kroky

* [Přehled služby Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Nejčastější dotazy k nastavení a datovému roamingu](enterprise-state-roaming-faqs.md)
* [Nastavení zásad skupiny a MDM pro synchronizaci nastavení](enterprise-state-roaming-group-policy-settings.md)
* [Referenční informace k nastavení roamingu pro Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Řešení potíží](enterprise-state-roaming-troubleshooting.md)
