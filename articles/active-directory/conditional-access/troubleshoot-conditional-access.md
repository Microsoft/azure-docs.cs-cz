---
title: Řešení potíží s přihlášením s podmíněným přístupem – Služba Azure Active Directory
description: Tento článek popisuje, co dělat, když zásady podmíněného přístupu vedou k neočekávaným výsledkům
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337438"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Poradce při potížích s přihlášením s podmíněným přístupem

Informace v tomto článku lze použít k řešení neočekávaných výsledků přihlášení souvisejících s podmíněným přístupem pomocí chybových zpráv a protokolu přihlášení Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Přerušení přihlášení podmíněného přístupu

Prvním způsobem je zkontrolovat chybovou zprávu, která se zobrazí. V případě problémů s přihlášením při používání webového prohlížeče obsahuje samotná chybová stránka podrobné informace. Tyto informace samy o sobě mohou popisovat, co je problém a které mohou navrhnout řešení.

![Chyba přihlášení – je vyžadováno vyhovující zařízení](./media/troubleshoot-conditional-access/image1.png)

Ve výše uvedené chybě se zobrazí zpráva, že k aplikaci lze přistupovat pouze ze zařízení nebo klientských aplikací, které splňují zásady správy mobilních zařízení společnosti. V tomto případě aplikace a zařízení nesplňují tyto zásady.

## <a name="azure-ad-sign-in-events"></a>Události přihlášení Azure AD

Druhou metodou pro získání podrobných informací o přerušení přihlášení je kontrola událostí přihlášení Služby Azure AD a zjistit, které zásady nebo zásady podmíněného přístupu byly použity a proč.

Další informace o problému naleznete kliknutím na **další podrobnosti** na úvodní chybové stránce. Kliknutím na **další podrobnosti** zobrazíte informace o řešení potíží, které jsou užitečné při vyhledávání událostí přihlášení služby Azure AD pro konkrétní událost selhání, kterou uživatel viděl, nebo při otevření incidentu podpory se společností Microsoft.

![Další podrobnosti z podmíněného přístupu přerušily přihlášení webového prohlížeče.](./media/troubleshoot-conditional-access/image2.png)

Chcete-li zjistit, které zásady podmíněného přístupu nebo zásady použity a proč postupujte takto.

1. Přihlaste se k **portálu Azure** jako globální správce, správce zabezpečení nebo globální čtečka.
1. Přejděte na**přihlašovací" služby** **Azure Active Directory** > .
1. Najděte událost, kterou chcete zkontrolovat. Přidáním nebo odebráním filtrů a sloupců můžete odfiltrovat nepotřebné informace.
   1. Přidáním filtrů zúžíte obor:
      1. **ID korelace,** pokud máte konkrétní událost prozkoumat.
      1. **Podmíněný přístup** zobrazíte selhání a úspěch zásad. Rozsah filtru zobrazit pouze selhání omezit výsledky.
      1. **Uživatelské jméno** pro zobrazení informací týkajících se konkrétních uživatelů.
      1. **Datum** vymezené do daného časového rámce.

   ![Výběr filtru podmíněného přístupu v protokolu přihlášení](./media/troubleshoot-conditional-access/image3.png)

1. Po přihlášení případě, který odpovídá selhání přihlášení uživatele byla nalezena vyberte **kartu Podmíněný přístup.** Na kartě Podmíněný přístup se zobrazí konkrétní zásady nebo zásady, které vedly k přerušení přihlášení.
   1. Informace na kartě **Poradce při potížích a podpoře** mohou poskytnout jasný důvod, proč se přihlášení nezdařilo, například zařízení, které nesplňovalo požadavky na dodržování předpisů.
   1. Chcete-li prozkoumat další, přejděte na konfiguraci zásad kliknutím na **název zásady**. Kliknutím na **název zásady** se zobrazí uživatelské rozhraní konfigurace zásad pro vybranou zásadu pro kontrolu a úpravy.
   1. **Podrobnosti o** **uživateli klienta** a zařízení, které byly použity pro posouzení zásad podmíněného přístupu, jsou k dispozici také na kartách **Základní informace**, **Umístění**, Informace **o zařízení**, **Podrobnosti o ověřování**a Další **podrobnosti** události přihlášení.

   ![Karta Podmíněný přístup k událostem přihlášení](./media/troubleshoot-conditional-access/image5.png)

Pokud informace v události nestačí k pochopení výsledků přihlášení nebo upravit zásady pro dosažení požadovaných výsledků, pak může být otevřen incident podpory. Přejděte na kartu Poradce **při potížích a podpoře** této události přihlášení a vyberte **vytvořit novou žádost o podporu**.

![Karta Poradce při potížích a podpora události přihlášení](./media/troubleshoot-conditional-access/image6.png)

Při odesílání incidentu zadejte ID žádosti a čas a datum z události přihlášení v podrobnostech o odeslání incidentu. Tyto informace umožní podpoře společnosti Microsoft najít událost, která vás znepokojuje.

### <a name="conditional-access-error-codes"></a>Kódy chyb podmíněného přístupu

| Kód chyby přihlášení | Řetězec chyby |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsnotanApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Další kroky

- [Sestavy aktivit přihlašování na portálu Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Poradce při potížích s podmíněným přístupem pomocí nástroje Co-li](troubleshoot-conditional-access-what-if.md)
- Doporučené postupy pro [podmíněný přístup ve službě Azure Active Directory](best-practices.md)
