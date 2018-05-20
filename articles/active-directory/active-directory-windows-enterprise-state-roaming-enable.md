---
title: Povolit Enterprise State Roaming v Azure Active Directory | Microsoft Docs
description: Nejčastější dotazy o nastavení Enterprise State Roaming v zařízení se systémem Windows. Enterprise State Roaming poskytuje uživatelům v jednotném rozhraní mezi jejich zařízení se systémem Windows a snižuje čas potřebný pro konfiguraci nové zařízení.
services: active-directory
keywords: Stav Enterprise roaming, cloudu systému windows, jak povolit roaming stavu enterprise
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.openlocfilehash: dba749b6d85898e6438ce1160b9bf6eaff6f4ac9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Povolení služby Enterprise State Roaming v Azure Active Directory
Enterprise State Roaming je k dispozici pro všechny organizace pomocí Azure AD Premium nebo Enterprise Mobility + Security (EMS) licence. Další informace o tom, jak získat předplatné Azure AD najdete v tématu [stránku produktů Azure AD](https://azure.microsoft.com/services/active-directory).

Když povolíte Enterprise State Roaming, vaše organizace je automaticky přiděleno licenci volné, omezeného použití ochrany Azure Rights Management z Azure Information Protection. Tato bezplatná registrace je omezený na šifrování a dešifrování nastavení podnikových a synchronizaci tím Enterprise State Roaming dat aplikací. Musíte mít [na placené předplatné](https://azure.microsoft.com/pricing/details/information-protection/) používat úplné funkce služby Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Chcete-li povolit Enterprise State Roaming

1. Přihlaste se k [centra pro správu Azure AD](https://aad.portal.azure.com/).

2. Vyberte **Azure Active Directory** &gt; **zařízení** &gt; **nastavení zařízení**.

3. Vyberte **uživatelé může synchronizovat nastavení a dat aplikací mezi zařízeními**. Další informace najdete v tématu [postup konfigurace nastavení zařízení](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![Obrázek nastavení zařízení s názvem uživatelů může synchronizovat nastavení a dat aplikací mezi zařízeními](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Pro zařízení Windows 10 pomocí služby Enterprise State Roaming zařízení musí ověřit pomocí služby Azure AD identity. Pro zařízení, které jsou připojené ke službě Azure AD je primární přihlášení identitu uživatele svou identitu Azure AD, takže není nutná žádná další konfigurace. Pro zařízení, která používají místní služby Active Directory, musí správce IT [připojení zařízení připojených k doméně ke službě Azure AD pro Windows 10 vyskytne](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Úložiště dat
Enterprise State Roaming dat je hostovaná v jednom nebo více [oblastí Azure](https://azure.microsoft.com/regions/) že nejvhodnější správně zarovnané s hodnotou země nebo oblast v instanci služby Azure Active Directory. Enterprise State Roaming dat je rozdělený do oddílů založené na tři hlavní zeměpisné oblasti: Severní Americe, regionu EMEA a APAC. Enterprise State Roaming dat pro klienta je místně umístěn v zeměpisné oblasti a v oblastech se nereplikuje.  Příklad:
Hodnota země nebo oblast | má svá data hostovány v
---------------------|-------------------------
EMEA země, jako je například "Francie" nebo "Zambie" | jeden nebo z regionů Azure v rámci Evropa 
Severní Ameriky země, jako je například "USA" nebo "Kanada" | jeden nebo více z regionů Azure v rámci USA
APAC země, jako je například "Austrálie" nebo "Nový Zéland" | jeden nebo více z regionů Azure v rámci Asie
Jižní Ameriky a Antarktida oblastí | jeden nebo více oblastech Azure v rámci USA

Hodnota země nebo oblast je nastaven jako součást procesu vytváření adresáře Azure AD a nemůže být upravena následně. Pokud potřebujete další podrobnosti v umístění úložiště vaše data, soubor lístek s [podporu Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Zobrazit stav synchronizace zařízení na uživatele
Následujícím postupem zobrazíte zprávu o stavu synchronizace zařízení na uživatele.

1. Přihlaste se k [centra pro správu Azure AD](https://aad.portal.azure.com/).

2. Vyberte **Azure Active Directory** &gt; **uživatelů a skupin** &gt; **všichni uživatelé**.

3. Vyberte uživatele a pak vyberte **zařízení**.

4. V části **zobrazit**, vyberte **zařízení synchronizaci nastavení a dat aplikací** zobrazit stav synchronizace.
  
  ![Obrázek nastavení zařízení synchronizace dat](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Pokud zařízení synchronizace pro tohoto uživatele se zobrazí zařízení, jak je vidět tady.
  
  ![Obrázek zařízení synchronizaci sloupcovém dat](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Uchovávání dat
Dokud je manuálně odstraněn, nebo dokud dotyčném dat je vyhodnocen jako zastaralé mají být uchována data synchronizovat do Azure pomocí Enterprise State Roaming. 

### <a name="explicit-deletion"></a>Explicitní odstranění
Explicitní odstranění je, když správce Azure odstraní uživatele nebo adresář nebo jinak žádostí explicitně že dat má být odstraněn.

* **Odstranění uživatele**: Při odstranění uživatele ve službě Azure AD je uživatelský účet, roaming dat odstraněny po 90 na 180 dní. 
* **Odstranění adresáře**: odstranění celého adresáře ve službě Azure AD je okamžitou operace. Všechna nastavení data související s které je adresáře odstraněny po 90 na 180 dní. 
* **Na žádost o odstranění**: Pokud chce správce Azure AD ručně odstranit data nebo data nastavení konkrétního uživatele, může správce souboru lístek s [podporu Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Odstranění zastaralých dat
Data, která nebyla získat přístup k jeden rok ("dobu uchování"), budou považovány za zastaralé a budou odstraněny z Azure. Doba uchovávání se může změnit, ale nesmí být menší než 90 dnů. Zastaralá data mohou být konkrétní sadu nastavení Windows/aplikace nebo všechna nastavení pro uživatele. Příklad:

* Pokud žádná zařízení přistupovat k kolekci konkrétní nastavení (například aplikace je odebráno ze zařízení nebo skupinu nastavení, jako je například "Motivu" vypnutá pro všechny uživatele zařízení), pak tuto kolekci zastaralé se stane po dobu uchování a může dojít k odstranění . 
* Pokud je uživatel vypnul nastavení synchronizace ve všech jejich zařízeních, pak budou mít přístup žádná nastavení data a všechna data nastavení pro tohoto uživatele bude zastaralé a budou odstraněny po dobu uchování. 
* Pokud správce Azure AD directory vypne Enterprise State Roaming pro celý adresář, pak všechny uživatele v, že adresář se zastaví synchronizaci nastavení a všechna nastavení data pro všechny uživatele bude zastaralé a budou odstraněny po dobu uchování. 

### <a name="deleted-data-recovery"></a>Obnovení odstraněných dat
Zásady uchovávání dat se nedá konfigurovat. Jakmile data je trvale odstraněn, není použitelná pro obnovení. Nastavení dat je však odstraněn pouze z Azure, nikoli z zařízení koncového uživatele. Pokud žádné zařízení později znovu připojí ke službě Enterprise State Roaming, jsou nastavení znovu synchronizovat a uložených v Azure.

## <a name="related-topics"></a>Související témata
* [Přehled Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Nastavení a datový roaming – nejčastější dotazy](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Nastavení zásad a správu mobilních zařízení pro synchronizaci nastavení skupiny](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Odkaz nastavení roamingu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Řešení potíží](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
