---
title: Testovací jednotka nabídky aplikace Azure | Azure Marketplace
description: Postup konfigurace testovací jednotky pro nabídku aplikací Azure na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826123"
---
# <a name="azure-applications-test-drive-tab"></a>Karta Azure Application test disk

Karta testovací jednotka vám poskytne zkušební prostředí pro vaše zákazníky.

## <a name="test-drive-benefits"></a>Výhody testovacích jednotek

Vytváření zkušebního prostředí pro vaše zákazníky je osvědčeným postupem, jak zajistit, aby si mohli koupit s jistotou. Z dostupných možností zkušební verze je testovací jednotka nejúčinnější při generování vysoce kvalitních potenciálních zákazníků a zvýšené konverze těchto zájemců.

Zákazníkům nabízí praktickou zkušební verzi klíčových funkcí a výhod vašeho produktu, které jsou uvedené ve scénáři implementace ve skutečném nasazení.

## <a name="how-a-test-drive-works"></a>Jak funguje testovací jednotka

Potenciální zákazník vyhledá a zjistí vaši aplikaci na webu Marketplace. Zákazník se přihlásí a bude souhlasit s podmínkami použití. V tomto okamžiku zákazník obdrží předem nakonfigurovaná prostředí, aby se mohl pokusit o určitý počet hodin, zatímco obdržíte vysoce kvalifikovaný zájemce, se kterým budete pokračovat. Další informace najdete v tématu [co je Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) .

## <a name="setting-up-a-test-drive"></a>Nastavení testovací jednotky

Pomocí následujícího postupu povolte a nakonfigurujte testovací jednotku.

### <a name="to-enable-a-test-drive"></a>Povolení testovací jednotky:

1. V části **Nová nabídka**vyberte kartu **testovací jednotka** .
2. V části **testovací jednotka**vyberte možnost **Ano** pro **Povolení testovací jednotky**.

   ![Povolit testovací jednotku](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Konfigurace testovací jednotky:

Po povolení testovací jednotky vyplníte následující formuláře k nastavení testovací jednotky:
  
 - Podrobnosti
 - Technická konfigurace
 - Podrobnosti o předplatném nasazení testovacích jednotek

Další snímek obrazovky zobrazuje všechny formuláře testovacích jednotek. Hvězdička (*), která je připojena k názvu pole, označuje, že je požadováno. 

![Konfigurace testovací jednotky](./media/managed-app-configure-testdrive.png)

Následující tabulka popisuje pole, která jsou nutná k nastavení testovacích jednotek pro spravovanou aplikaci.  Pole připojená pomocí hvězdičky jsou povinná.

|      Pole         |  Popis      |
|  ---------------   |  ---------------  |
| **Popis\***  |  Popište, co se dá udělat na testovací jednotce. Tento popis můžete naformátovat pomocí základních značek HTML. Například &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;a nadpisy.                |
| **Ruční\* uživatele**  |  Nahrajte uživatelskou příručku, kterou můžou vaši zákazníci použít k procházení zkušeností testovacích jednotek. Tento dokument musí být soubor. PDF.    |
| **Ukázkové video testovacího disku** |  Volitelný návod k videu testovacího disku. Zákazník může toto video přehrát předtím, než si vyzkouší testovací jednotku. Zadejte adresu URL videa na YouTube nebo Vimeo. Pokud vyberete **+ přidat video**, zobrazí se výzva, abyste zadali následující informace:<ul><li>Name (Název)</li><li>zprostředkovatele identity</li><li>Miniatura (ve formátu PNG, 533 x 324 pixelů)</li></ul>  |
| **Instance\***      | Nakonfigurujte, kolik instancí chcete, v jaké oblasti a jak rychle můžou zákazníci získat testovací verzi. Další informace najdete v tématu [Postup publikování testovací jednotky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Doba trvání testovacích jednotek (hodiny)\*** | Zadejte celé číslo v hodinách. Povolený rozsah je od 1 do 999. |
| **\* šablonou ARM testovacích jednotek**     | Nahrajte komprimovaný soubor (ZIP), který obsahuje šablony Azure Resource Manager pro vaši aplikaci. Další informace najdete v tématu [Azure Resource Manager testovacích jednotek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Přístup k informacím\***          | Poskytněte informace o přístupu poté, co zákazník získá testovací jednotku. Například adresa URL pro přístup k testovací jednotce a informace o podpisu. . Tento popis můžete naformátovat pomocí základních značek HTML. Například &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;a nadpisy. |
| **ID předplatného Azure\***       | Tím se udělí přístup ke službám Azure a Azure Portal. Předplatné je místo, kde se oznamuje využití prostředků, a účtují se služby. Pokud ještě nemáte samostatné předplatné Azure jenom pro testovací jednotky, vytvořte předplatné.  |
| **ID tenanta Azure AD\***          | Poskytněte stávajícího klienta v Azure Active Directory nebo vytvořte tenanta pro tuto testovací jednotku.  |
| **ID Aplikace Azure AD\***             | Vytvořte a zaregistrujte novou aplikaci. Společnost Microsoft používá tuto aplikaci k provádění operací s instancí testovacích jednotek.  |
| **Aplikace Azure AD Key\***            | Vytvořte pro aplikaci ověřovací klíč a vložte ho do tohoto pole.   |
|  |  |

Až zadáte všechny požadované informace, vyberte **Uložit** a dokončete nastavování testovacích jednotek.


## <a name="next-steps"></a>Další kroky

[Karta Marketplace](./cpp-marketplace-tab.md)
