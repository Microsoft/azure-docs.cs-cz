---
title: Povolení služby Enterprise State Roaming v Azure Active Directory | Dokumentace Microsoftu
description: Nejčastější dotazy ohledně Enterprise State Roaming nastavení v zařízení s Windows. Enterprise State Roaming uživatelům poskytuje jednotné prostředí v jejich zařízeních Windows a snižuje čas potřebný ke konfiguraci nových zařízení.
services: active-directory
keywords: Enterprise stav roamingu, cloudu systému windows, jak povolit roaming stavu enterprise
documentationcenter: ''
author: tanning
manager: daveba
editor: curtand
ms.subservice: devices
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ce6dc4cf98f2886eb4eed7049048d67599ac42e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207479"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Povolení služby Enterprise State Roaming v Azure Active Directory
Enterprise State Roaming je k dispozici pro všechny organizace s Azure AD Premium nebo Enterprise Mobility + Security (EMS) licence. Další informace o tom, jak získat předplatné Azure AD, najdete v článku [stránce produktu Azure AD](https://azure.microsoft.com/services/active-directory).

Když povolíte Enterprise State Roaming, vaše organizace je automaticky udělena omezeného použití, bezplatné licence pro ochranu Azure Rights Management ze služby Azure Information Protection. Tuto bezplatnou registraci je omezená na šifrování a dešifrování nastavení organizace a synchronizovaný se službou Enterprise State Roaming dat aplikací. Musíte mít [přechod na placené předplatné](https://azure.microsoft.com/pricing/details/information-protection/) používat všechny funkce služby Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Chcete-li povolit Enterprise State Roaming

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com/).

1. Vyberte **Azure Active Directory** &gt; **zařízení** &gt; **Enterprise State Roaming**.

1. Vyberte **uživatelé můžou synchronizovat nastavení a data aplikací na zařízeních**. Další informace najdete v tématu [jak nakonfigurovat nastavení zařízení](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Obrázek nastavení zařízení s názvem uživatelé můžou synchronizovat nastavení a data aplikací na zařízeních](./media/enterprise-state-roaming-enable/device-settings.png)
  
Pro zařízení s Windows 10 k používání služby Enterprise State Roaming zařízení musí ověřit pomocí identity Azure AD. Pro zařízení, která jsou připojená k Azure AD je primární přihlašovací identitu uživatele svoji identitu služby Azure AD, takže není nutná žádná další konfigurace. Zařízení, která používají místní služby Active Directory, musí správce IT [zařízení připojená k hybridní službě Azure Active Directory konfigurovat](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual-steps). 

## <a name="data-storage"></a>Úložiště dat
Enterprise State Roaming dat je hostovaná v jednom nebo více [oblastí Azure](https://azure.microsoft.com/regions/) , že nejlepší bylo v souladu s země/oblast hodnotu nastavenou v instanci Azure Active Directory. Enterprise State Roaming data jsou rozdělená podle tři hlavní zeměpisné oblasti: Severní Amerika, EMEA a APAC. Enterprise State Roaming dat pro tenanta se nachází na místně geografické oblasti a není replikován napříč oblastmi.  Příklad:

Země/oblast hodnoty | má konání svá data
---------------------|-------------------------
EMEA země, třeba (Francie) nebo Zambie | Jeden nebo více oblastí Azure v Evropě 
Severní Ameriky země, jako jsou USA nebo Kanadě | jeden nebo více oblastí Azure v rámci USA
APAC země, třeba Austrálie a Nového Zélandu | jeden nebo více oblastí Azure v rámci Asie
Jižní Ameriky a Antarktida oblastí | jeden nebo více oblastí Azure v rámci USA

Země/oblast hodnota je nastavena jako součást procesu vytvoření adresáře Azure AD a následně nelze upravit. Pokud potřebujete další podrobnosti na vaše umístění úložiště dat, vytvořte lístek [podpory Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Zobrazit stav synchronizace zařízení na uživatele
Postupujte podle těchto kroků pro zobrazení zprávy o stavu synchronizace zařízení na uživatele.

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com/).

1. Vyberte **Azure Active Directory** &gt; **uživatelé** &gt; **všichni uživatelé**.

1. Vyberte uživatele a pak vyberte **zařízení**.

1. V části **zobrazit**vyberte **zařízení, která synchronizují nastavení a data aplikací** zobrazíte stav synchronizace.
  
  ![Obrázek nastavení synchronizace dat zařízení](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Pokud zařízení, která synchronizují pro tohoto uživatele se zobrazí zařízení, jak je znázorněno zde.
  
  ![Obrázek zařízení synchronizace úložiště se sloupcovou strukturou dat](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Uchovávání dat
Data synchronizují do cloudu Microsoftu pomocí Enterprise State Roaming se uchovávají, dokud je ručně neodstraníte nebo dokud dotyčný dat je určena jako zastaralé. 

### <a name="explicit-deletion"></a>Explicitní odstranění
Explicitní odstranění je, když Azure správce odstraní uživatele nebo adresář nebo jinak požadavků explicitně, že data se odstraní.

* **Odstranění uživatele**: Při odstranění uživatele ve službě Azure AD je uživatelský účet roamingová data odstraněny po 90 až 180 dní. 
* **Odstranění adresáře**: Odstraňuje se celý adresář ve službě Azure AD je okamžitě operace. Nastavení data související s, které adresáře odstraněny po 90 až 180 dní. 
* **V žádosti o odstranění**: Pokud chce správce služby Azure AD ručně odstranit data nebo data nastavení konkrétního uživatele, Správce může lístek pomocí [podpory Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Odstranění zastaralých dat
Data, která se nepřistupovalo po dobu jednoho roku ("uchovávání doba"), bude zacházeno jako zastaralé a můžou se odstranit z cloudu Microsoftu. Doba uchovávání se může změnit, ale nesmí být dřívější než 90 dní. Zastaralá data může být konkrétní sadu nastavení Windows/aplikace nebo všechna nastavení pro uživatele. Příklad:

* Pokud se žádná zařízení získat přístup ke kolekci konkrétní nastavení (například aplikace se odebere ze zařízení nebo skupinu nastavení, jako je například "Motiv" je zakázaná pro všechny uživatele zařízení), pak tuto kolekci zastarávají po uplynutí doby uchování a může dojít k odstranění . 
* Pokud uživatele po vypnutí nastavení synchronizace ve všech jeho zařízeních, pak žádná data nastavení budou mít přístup, a všechna nastavení data pro tohoto uživatele se stane zastaralou a po dobu uchování, můžou se odstranit. 
* Pokud správce Azure AD directory vypne Enterprise State Roaming pro celý adresář, pak všichni uživatelé adresáře se zastaví, synchronizovat nastavení a všechna nastavení data pro všechny uživatele budou zastaralá a můžou se odstranit po uplynutí doby uchování. 

### <a name="deleted-data-recovery"></a>Obnovení odstraněných dat
Zásady uchovávání dat se nedá konfigurovat. Jakmile se data se trvale odstraní, se nedá vrátit zpátky. Však nastavení data se odstraní pouze z cloudu Microsoftu, ne ze zařízení koncového uživatele. Pokud žádné zařízení znovu připojí později služby Enterprise State Roaming, jsou nastavení znovu synchronizované a uložená v cloudu Microsoftu.

## <a name="next-steps"></a>Další postup

* [Přehled Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Nastavení a datovému roamingu – nejčastější dotazy](enterprise-state-roaming-faqs.md)
* [Nastavení zásad a MDM pro synchronizaci nastavení skupiny](enterprise-state-roaming-group-policy-settings.md)
* [Informace o cestovní nastavení Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Řešení potíží](enterprise-state-roaming-troubleshooting.md)
