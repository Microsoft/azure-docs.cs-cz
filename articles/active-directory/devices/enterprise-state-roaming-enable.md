---
title: Povolení služby Enterprise State Roaming v Azure Active Directory
description: Nejčastější dotazy týkající se nastavení Enterprise State Roaming v zařízeních s Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b554fbef63f23b3540fe49e5c45976122add25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89268600"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Povolení služby Enterprise State Roaming v Azure Active Directory

Enterprise State Roaming je k dispozici pro libovolnou organizaci s licencí Azure AD Premium nebo Enterprise Mobility + Security (EMS). Další informace o tom, jak získat předplatné služby Azure AD, najdete na [stránce produktu Azure AD](https://azure.microsoft.com/services/active-directory).

Pokud povolíte Enterprise State Roaming, vaše organizace vám automaticky udělí bezplatnou, časově omezená licenci pro Azure Rights Management Protection od Azure Information Protection. Toto bezplatné předplatné je omezené na šifrování a dešifrování podnikových nastavení a dat aplikací synchronizovaných pomocí Enterprise State Roaming. Abyste mohli využívat všechny možnosti služby Azure Rights Management, musíte mít [placené předplatné](https://azure.microsoft.com/pricing/details/information-protection/) .

> [!NOTE]
> Tento článek se týká starší verze prohlížeče založeného na HTML v Microsoft Edge, který se spouští s Windows 10 v červenci 2015. Článek neplatí pro nový prohlížeč založený na Microsoft Edge chrom vydaný 15. ledna 2020. Další informace o chování synchronizace pro nové Microsoft Edge najdete v článku o [synchronizaci Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Postup povolení Enterprise State Roaming

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com/).
1. Vyberte **Azure Active Directory**  >  **zařízení**  >  **Enterprise State Roaming**.
1. Vyberte možnost **Uživatelé můžou synchronizovat nastavení a data aplikací napříč zařízeními**. Další informace najdete v tématu [Konfigurace nastavení zařízení](./device-management-azure-portal.md).
  
   ![Obrázek nastavení zařízení s označením uživatelé můžou synchronizovat nastavení a data aplikací napříč zařízeními.](./media/enterprise-state-roaming-enable/device-settings.png)
  
Aby zařízení s Windows 10 mohl používat službu Enterprise State Roaming, musí se zařízení ověřit pomocí identity Azure AD. Pro zařízení, která jsou připojená k Azure AD, je primární přihlašovací identita uživatele svou identitou Azure AD, takže se nevyžaduje žádná další konfigurace. U zařízení, která používají místní službu Active Directory, musí správce IT [nakonfigurovat zařízení připojená k hybridnímu Azure Active Directory](./hybrid-azuread-join-plan.md). 

## <a name="data-storage"></a>Úložiště dat

Data Enterprise State Roaming se hostují v jedné nebo několika [oblastech Azure](https://azure.microsoft.com/regions/) , které jsou nejlépe zarovnané s hodnotou země nebo oblasti nastavenou v instanci Azure Active Directory. Data Enterprise State Roaming jsou rozdělená na oddíly na základě tří hlavních geografických oblastí: Severní Amerika, EMEA a APAC. Data Enterprise State Roaming pro tenanta se místně nacházejí v geografickém regionu a nereplikují se mezi regiony.  Například:

| Hodnota země/oblasti | hostuje svá data v |
| -------------------- | ------------------------ |
| Země nebo oblast EMEA, například Francie nebo Zambie | Jedna nebo více oblastí Azure v rámci Evropy |
| Země nebo oblast v Severní Americe, například USA nebo Kanada | Jedna nebo více oblastí Azure v USA |
| APAC země/oblast, jako je Austrálie nebo Nový Zéland | Jedna nebo více oblastí Azure v Asii |
| Oblasti Jižní Ameriky a Antarktida | Jedna nebo více oblastí Azure v USA |

Hodnota země nebo oblast se nastaví jako součást procesu vytváření adresáře služby Azure AD a nedá se následně upravit. Pokud potřebujete další podrobnosti o umístění úložiště dat, zapněte lístek s [podporou Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Zobrazit stav synchronizace zařízení podle uživatele

Pomocí těchto kroků můžete zobrazit sestavu stavu synchronizace zařízení pro jednotlivé uživatele.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com/).
1. Vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. Vyberte uživatele a pak vyberte **zařízení**.
1. V části **Zobrazit**vyberte **zařízení, která synchronizují nastavení a data aplikací** , a zobrazte tak stav synchronizace.
  
   ![Obrázek nastavení synchronizace dat zařízení](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Pokud se zařízení synchronizují pro tohoto uživatele, uvidíte zařízení, jak je znázorněno zde.
  
   ![Obrázek sloupcových dat synchronizace zařízení](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Uchovávání dat

Data synchronizovaná do cloudu Microsoftu pomocí Enterprise State Roaming se uchovávají, dokud je ručně neodstraníte nebo dokud se nezjistí, že jsou data zastaralá. 

### <a name="explicit-deletion"></a>Explicitní odstranění

Explicitní odstranění je v případě, že správce Azure odstraní uživatele nebo adresář nebo jinak požadavky explicitně vyžádá, aby se data odstranila.

* **Odstranění uživatele**: když se uživatel ve službě Azure AD odstraní, data roamingu uživatelského účtu se odstraní po 90 až 180 dnů. 
* **Odstranění adresáře**: odstranění celého adresáře ve službě Azure AD je okamžitá operace. Všechna data nastavení přidružená k tomuto adresáři se odstraní po 90 až 180 dnů. 
* Při **odstraňování žádosti**: Pokud chce správce Azure AD ručně odstranit data konkrétního uživatele nebo data nastavení, může správce odeslat lístek s [podporou Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Odstranění zastaralých dat

Data, která se nezískala po dobu jednoho roku ("doba uchování"), budou považována za zastaralá a je možné je odstranit z cloudu Microsoftu. Doba uchovávání se může změnit, ale nebude nižší než 90 dní. Zastaralá data můžou být konkrétní sadou nastavení Windows/aplikace nebo všech nastavení pro uživatele. Například:

* Pokud žádná zařízení nepřistupuje ke konkrétní kolekci nastavení (například když je aplikace ze zařízení odebraná nebo pokud je skupina nastavení, jako je "motiv", zakázaná pro všechna zařízení uživatele), pak se tato kolekce po dobu uchování zastará a může se odstranit. 
* Pokud uživatel vypnul nastavení na všech svých zařízeních, nebude k dispozici žádná data nastavení a veškerá data nastavení pro tohoto uživatele budou zastaralá a můžou se po uplynutí doby uchování odstranit. 
* Pokud se u celého adresáře Enterprise State Roaming Správce adresáře Azure AD, pak všichni uživatelé v tomto adresáři zastaví synchronizaci nastavení a všechna data nastavení pro všechny uživatele budou zastaralá a můžou se po uplynutí doby uchování odstranit. 

### <a name="deleted-data-recovery"></a>Obnovení odstraněných dat

Zásady uchovávání dat se nedají konfigurovat. Po trvalém odstranění dat není tato data obnovitelné. Data nastavení se ale odstraní jenom z cloudu Microsoftu, ne ze zařízení koncového uživatele. Pokud se nějaké zařízení později znovu připojí ke službě Enterprise State Roaming, nastavení se znovu synchronizují a uloží v cloudu Microsoftu.

## <a name="next-steps"></a>Další kroky

* [Přehled služby Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Nejčastější dotazy k nastavení a datovému roamingu](enterprise-state-roaming-faqs.md)
* [Zásady skupiny a nastavení MDM pro synchronizaci nastavení](enterprise-state-roaming-group-policy-settings.md)
* [Referenční informace k nastavení roamingu pro Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Řešení potíží](enterprise-state-roaming-troubleshooting.md)