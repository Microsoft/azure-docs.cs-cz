---
title: Vytvořte Dynamics 365 pro Customer Engagement & nabídku PowerApps na Microsoft AppSource (Azure Marketplace).
description: Vytvořte Dynamics 365 pro Customer Engagement & nabídku PowerApps na Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820366"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Postup 365 vytvoření nabídky PowerApps pro Customer Engagement &

Tento článek popisuje, jak vytvořit Dynamics 365 for Customer Engagement & nabídky PowerApps. Všechny nabídky pro Dynamics 365 přecházejí z našeho procesu certifikace. Zkušební prostředí umožňuje uživatelům nasadit vaše řešení do živého prostředí Dynamics 365.

Než začnete, vytvořte si účet komerčního tržiště v [partnerském centru](partner-center-portal/create-account.md) a ujistěte se, že je zaregistrovaný v programu komerčního tržiště.

## <a name="before-you-begin"></a>Než začnete

Přečtěte si téma [plánování nabídky Dynamics 365](marketplace-dynamics-365.md). Vysvětlete technické požadavky na tuto nabídku a seznam informací a prostředků, které budete potřebovat při vytváření.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Dynamics 365 pro zákaznickou zapojení & PowerApps**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Zobrazuje levé podokno možnosti nabídky a tlačítko Nová nabídka se zákaznickou rezervací SELECT.":::

> [!IMPORTANT]
> Po publikování nabídky se všechny úpravy, které provedete v partnerském centru, zobrazí Microsoft AppSource až po opětovném publikování nabídky. Nezapomeňte po změně nabídky vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku a v případě potřeby v šablonách Azure Resource Manager.
- Použijte při tom jenom malá písmena a číslice. ID může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezeno na 50 znaků. Pokud je například vaším vydavatelem ID `testpublisherid` a zadáte **test-nabídka-1**, Webová adresa nabídky bude `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se nepoužívá v AppSource. Liší se od názvu nabídky a dalších hodnot zobrazených zákazníkům.
- Po výběru **vytvořit** se tento název nedá změnit.

Vyberte **vytvořit** a vygenerujte nabídku. Partnerské centrum otevře stránku **nastavení nabídky** .

## <a name="alias"></a>Alias

Zadejte popisný název, který použijeme k tomu, abychom na tuto nabídku odkazovali výhradně v partnerském centru. Alias nabídky (předem vyplněný s tím, co jste zadali při vytváření nabídky) se na webu Marketplace nepoužije a liší se od názvu nabídky, který se zákazníkům zobrazuje. Pokud chcete později aktualizovat název nabídky, přejděte na stránku se [seznamem nabídek](dynamics-365-customer-engage-offer-listing.md) .

## <a name="setup-details"></a>Podrobnosti o nastavení

**Jak chcete, aby potenciální zákazníci mohli pracovat s touto nabídkou**, vyberte možnost, kterou chcete pro tuto nabídku použít.

- **Získejte ho hned (zdarma)** – Seznamte se s vaší nabídkou zdarma pro zákazníky.
- **Bezplatná zkušební verze (výpis)** – Seznamte se s vaší nabídkou pro zákazníky s odkazem na bezplatnou zkušební verzi. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem.

    > [!NOTE]
    > Tokeny, které vaše aplikace získá prostřednictvím zkušebního připojení, se dají použít jenom k získání informací o uživateli prostřednictvím Azure Active Directory (Azure AD) k automatizaci vytváření účtů ve vaší aplikaci. Pro ověřování pomocí tohoto tokenu se nepodporují účty Microsoft.

- **Kontaktujte mě** – shromáždění zákaznických informací o zákaznících tak, že se připojíte k systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v tématu [zájemci zákazníků](#customer-leads).

## <a name="test-drive"></a>Testovací jednotka

Testovací jednotka představuje skvělý způsob, jak předprezentovat vaši nabídku potenciálním zákazníkům tím, že jim poskytne přístup k předkonfigurovanému prostředí po dobu určitého počtu hodin. Nabídka testovacích jednotek má za následek vyšší míru převodu a generuje vysoce kvalifikované potenciální zákazníky. Pokud se chcete dozvědět víc, začněte tím, [co je testovací jednotka?](what-is-test-drive.md).

> [!TIP]
> Testovací jednotka se liší od bezplatné zkušební verze. Můžete nabízet buď testovací, bezplatnou zkušební verzi, nebo obojí. Poskytují zákazníkům s vaším řešením pevně stanovené časové období. Testovací jednotka ale taky obsahuje praktickou a samoobslužnou prohlídku klíčových funkcí vašeho produktu a výhod, které se provedou ve scénáři implementace reálného světa.

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** a vyberte **typ testovací jednotky**. Testovací jednotku budete konfigurovat později. Pomocí testovacích jednotek musíte také nakonfigurovat nabídku zákaznických zájemců (viz další oddíl). Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

## <a name="customer-leads"></a>Potenciální zákazníci

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Další informace najdete v tématu [potenciální zákazníci z komerční nabídky na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Než budete pokračovat na další kartu v nabídce vlevo na **Další, vyberte** **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

- [Konfigurovat vlastnosti nabídky](dynamics-365-customer-engage-properties.md)
- [Osvědčené postupy nabídky](gtm-offer-listing-best-practices.md)