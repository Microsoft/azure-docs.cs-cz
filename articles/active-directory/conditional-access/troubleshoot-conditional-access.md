---
title: Řešení potíží s přihlašováním pomocí podmíněného přístupu – Azure Active Directory
description: Tento článek popisuje, co dělat, když zásady podmíněného přístupu vedou k neočekávaným výsledkům.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6778b556795f4e079100f1a7bcbb8b9465e9e315
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88032964"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Řešení potíží s přihlášením s využitím podmíněného přístupu

Informace v tomto článku se dají použít k řešení neočekávaných přihlašovacích údajů souvisejících s podmíněným přístupem, které využívají chybové zprávy a protokol přihlášení k Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Přerušení přihlášení k podmíněnému přístupu

První způsob je zkontrolovat zobrazenou chybovou zprávu. Pro problémy, které se přihlašují při použití webového prohlížeče, má chybová stránka obsahuje podrobné informace. Tyto informace mohou být popsány v části problém a může navrhnout řešení.

![Vyžaduje se přihlášení zařízení vyhovujícího chybám.](./media/troubleshoot-conditional-access/image1.png)

Ve výše uvedené chybě zpráva uvádí, že aplikace je dostupná jenom ze zařízení nebo klientských aplikací, které splňují zásady správy mobilních zařízení společnosti. V takovém případě aplikace a zařízení nesplňují tyto zásady.

## <a name="azure-ad-sign-in-events"></a>Události přihlášení ke službě Azure AD

Druhý způsob, jak získat podrobné informace o přerušení přihlašování, je zkontrolovat události přihlášení služby Azure AD, které vám pomohou zjistit, které zásady nebo zásady podmíněného přístupu byly aplikovány a proč.

Další informace o problému najdete kliknutím na **Další podrobnosti** na úvodní stránce s chybou. Kliknutím na **Další podrobnosti** zobrazíte informace o řešení problémů, které jsou užitečné při hledání událostí přihlášení služby Azure AD pro konkrétní událost selhání, kterou uživatel viděl, nebo při otevření incidentu podpory u Microsoftu.

![Další podrobnosti z podmíněného přístupu přerušily přihlášení k webovému prohlížeči.](./media/troubleshoot-conditional-access/image2.png)

Pokud chcete zjistit, které zásady nebo zásady podmíněného přístupu se nastavily, a proč postupujte následovně.

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo globální čtecí zařízení.
1. Přejděte k **Azure Active Directory**  >  **přihlášení**.
1. Najděte událost pro přihlášení, která se má zkontrolovat. Přidejte nebo odeberte filtry a sloupce pro odfiltrování zbytečných informací.
   1. Přidejte filtry pro zúžení rozsahu:
      1. **ID korelace** , pokud máte konkrétní událost k prozkoumání.
      1. **Podmíněný přístup** , který vám umožní zobrazit selhání zásad a úspěch. Nastavte obor filtru tak, aby zobrazoval pouze chyby pro omezení výsledků.
      1. **Uživatelské jméno** pro zobrazení informací týkajících se konkrétních uživatelů.
      1. **Datum** vymezené na příslušný časový rámec.

   ![Výběr filtru podmíněného přístupu v protokolu přihlášení](./media/troubleshoot-conditional-access/image3.png)

1. Jakmile se najde přihlašovací událost, která odpovídá neúspěšnému přihlášení uživatele, vyberte kartu **podmíněný přístup** . Na kartě podmíněný přístup se zobrazí konkrétní zásady nebo zásady, které vedly k přerušení přihlášení.
   1. Informace na kartě **Poradce při potížích a podpoře** můžou mít jasný důvod, proč se nezdařilo přihlášení jako zařízení, které nevyhovělo požadavkům na dodržování předpisů.
   1. Pokud chcete prozkoumat další postup, přejděte k podrobnostem o konfiguraci zásad kliknutím na **název zásady**. Kliknutím na **název zásady** zobrazíte uživatelské rozhraní konfigurace zásad pro vybranou zásadu pro kontrolu a úpravy.
   1. Podrobnosti **o uživateli** a **zařízení** klienta, které byly použity pro vyhodnocení zásad podmíněného přístupu, jsou k dispozici také na kartách **základní informace**, **umístění**, **informace o zařízení**, **Podrobnosti o ověřování**a další karty **podrobností** události přihlášení.

### <a name="policy-details"></a>Podrobnosti zásady

Výběr tří teček na pravé straně zásady v události přihlášení přináší podrobnosti o zásadách. To správcům poskytuje další informace o tom, proč se zásada úspěšně použila.

   ![Karta pro podmíněný přístup události přihlášení](./media/troubleshoot-conditional-access/image5.png)

   ![Podrobnosti zásady (Preview)](./media/troubleshoot-conditional-access/policy-details.png)

Na levé straně najdete podrobnosti shromážděné při přihlášení a na pravé straně najdete podrobnosti o tom, jestli tyto podrobnosti splňují požadavky na použité zásady podmíněného přístupu. Zásady podmíněného přístupu se použijí jenom v případě, že jsou splněné nebo nejsou nakonfigurované všechny podmínky.

Pokud informace v události nestačí pro pochopení výsledků přihlášení nebo úpravou zásad za účelem získání požadovaných výsledků, může být otevřen incident podpory. Přejděte na kartu pro **odstraňování potíží a podporu** přihlašovací události a vyberte **vytvořit novou žádost o podporu**.

![Karta Poradce při potížích a podpoře události přihlášení](./media/troubleshoot-conditional-access/image6.png)

Při odesílání incidentu zadejte ID žádosti a čas a datum z události přihlášení v podrobnostech o odeslání incidentu. Tyto informace umožní podpoře Microsoftu najít událost, o kterou máte obavy.

### <a name="conditional-access-error-codes"></a>Kódy chyb podmíněného přístupu

| Kód chyby přihlášení | Řetězec chyby |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Další kroky

- [Sestavy aktivit přihlašování na portálu Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Řešení potíží s podmíněným přístupem pomocí nástroje What If](troubleshoot-conditional-access-what-if.md)
- Osvědčené postupy pro [podmíněný přístup v Azure Active Directory](best-practices.md)
