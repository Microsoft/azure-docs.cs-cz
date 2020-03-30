---
title: Testovací jízda nabídky aplikací Azure | Azure Marketplace
description: Jak nakonfigurovat testovací disk pro nabídku aplikací Azure na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 2f35dd69c1f29350049ed8f62dd9cbb0e60a7718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289001"
---
# <a name="azure-applications-test-drive-tab"></a>Karta Testovací jednotka aplikací Azure

Karta Testovací jednotka slouží k poskytování zkušebního prostředí pro zákazníky.

## <a name="test-drive-benefits"></a>Výhody testovací jízdy

Vytvoření zkušebního prostředí pro vaše zákazníky je osvědčeným postupem, jak zajistit, že mohou nakupovat s důvěrou. Z dostupných zkušebních možností je Test Drive nejúčinnější při generování vysoce kvalitních potenciálních zákazníků a zvýšené konverzi těchto potenciálních zákazníků.

Poskytuje zákazníkům praktickou zkušební verzi klíčových funkcí a výhod vašeho produktu, která je demonstrována v reálném scénáři implementace.

## <a name="how-a-test-drive-works"></a>Jak testovací jízda funguje

Potenciální zákazník vyhledá vaši aplikaci na Marketplace a zjišťuje ji. Zákazník se přihlásí a souhlasí s podmínkami použití. V tomto okamžiku zákazník obdrží předem nakonfigurované prostředí, aby se pokusil o pevný počet hodin, zatímco obdržíte vysoce kvalifikovaného zájemce, se kterým chcete sledovat. Další informace naleznete v tématu [Co je testdrive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Nastavení zkušební jízdy

Pomocí následujících kroků povolte a nakonfigurujte testovací jednotku.

### <a name="to-enable-a-test-drive"></a>Povolení zkušební jízdy:

1. V části **Nová nabídka**vyberte kartu **Testovací jednotka.**
2. V části **Test Drive**vyberte **možnost Ano** pro povolení **testovací jednotky**.

   ![Povolení zkušební jízdy](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Konfigurace testovací jednotky:

Po povolení testovací jízdy vyplníte následující formuláře pro nastavení testovací jednotky:
  
 - Podrobnosti
 - Technická konfigurace
 - Podrobnosti o předplatném nasazení testovací jednotky

Při dalším zachycení obrazovky se zobrazí všechny formuláře testovací jednotky. Hvězdička (*) připojená k názvu pole označuje, že je požadováno. 

![Konfigurace testovací jednotky](./media/managed-app-configure-testdrive.png)

Následující tabulka popisuje pole potřebná k nastavení testovací jednotky pro spravovanou aplikaci.  Jsou povinná pole připojená hvězdičkou.

|      Pole         |  Popis      |
|  ---------------   |  ---------------  |
| **Popis\***  |  Popište, co lze provést na testovací jízdě. K formátování tohoto popisu můžete použít základní značky HTML. Například &lt;p&gt; &lt;,&gt; &lt;em&gt; &lt;,&gt; &lt;ul&gt;, li , ol a nadpisy.                |
| **Uživatelská příručka\***  |  Nahrajte uživatelskou příručku, kterou mohou zákazníci použít k procházení prostředí Testovací jízdy. Tento dokument musí být soubor pdf.    |
| **Ukázkové video testovací jednotky** |  Volitelný video návod testovací jízdy. Zákazník může toto video sledovat ještě před zkušební jízdou. Zadejte adresu URL videa na YouTube nebo Vimeo. Pokud vyberete **+ Přidat video**, budete vyzváni k zadání následujících informací:<ul><li>Name (Název)</li><li>zprostředkovatele identity</li><li>Miniatura (ve formátu PNG, 533 x 324 pixelů)</li></ul>  |
| **Instance\***      | Nakonfigurujte, kolik instancí chcete, v jakých oblastech a jak rychle mohou vaši zákazníci získat testovací jednotku. Další informace naleznete [v tématu Jak publikovat testovací disku](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Doba trvání zkušební jízdy (hodiny)\*** | Zadejte celé číslo pro počet hodin. Povolený rozsah je od 1 do 999. |
| **Šablona ARM testovací jednotky\***     | Nahrajte komprimovaný (.zip) soubor, který má vaše šablony Azure Resource Manager pro vaši aplikaci. Další informace najdete v [tématu Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Přístup k informacím\***          | Po zadání informací o testovací jízdě zákazníkem zadejte přístupové informace. Například adresa URL pro přístup k testovací jednotce a informace o podpisu. . K formátování tohoto popisu můžete použít základní značky HTML. Například &lt;p&gt; &lt;,&gt; &lt;em&gt; &lt;,&gt; &lt;ul&gt;, li , ol a nadpisy. |
| **Id předplatného Azure\***       | To uděluje přístup ke službám Azure a portálu Azure. Předplatné je místo, kde je hlášeno využití prostředků a služby se účtují. Pokud ještě nemáte samostatné předplatné Azure pro testovací jednotky jenom, vytvořte předplatné.  |
| **Id klienta Azure AD\***          | Zadejte existujícího klienta ve službě Azure Active Directory nebo vytvořte klienta pro tuto testovací jednotku.  |
| **ID aplikace Azure AD\***             | Vytvořte a zaregistrujte novou aplikaci. Společnost Microsoft používá tuto aplikaci k provádění operací s instancí testovací jednotky.  |
| **Klíč aplikace Azure AD\***            | Vytvořte ověřovací klíč pro aplikaci a vložte jej do tohoto pole.   |
|  |  |

Po zadání všech požadovaných informací dokončete nastavení testovací jednotky výběrem **možnosti Uložit.**


## <a name="next-steps"></a>Další kroky

[Karta Marketplace](./cpp-marketplace-tab.md)
