---
title: Azure Application nabídka testovací verze | Dokumentace Microsoftu
description: Jak nakonfigurovat testovací verze nabídky aplikace Azure na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: de4c8aecefce334889c3fa6790c0ba42673896c7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196667"
---
# <a name="azure-applications-test-drive-tab"></a>Karta testovací verze aplikace Azure

Na kartě testovací verze pro zajištění zkušební prostředí pro vaše zákazníky.

## <a name="test-drive-benefits"></a>Test jednotky výhody

Vytvoření zkušebního prostředí pro vaše zákazníky je osvědčeným postupem je zajistit, že můžete koupit bez obav. Možnosti zkušebních verzí dostupné Test Drive je nejúčinnější pro vysoce kvalitní generování potenciálních zákazníků a větší převodu těchto potenciálních zákazníků.

Zákazníkům přináší praktická, samostatně prováděného zkušební verzi klíčové funkce a výhody, které jsme vám ukázali ve scénáři Skutečná implementace váš produkt.

## <a name="how-a-test-drive-works"></a>Jak funguje testovací verze

Potenciální zákazník prohledá a zjistí aplikace na webu Marketplace. Zákazník přihlásí a souhlasí s podmínkami použití. V tomto okamžiku zákazník přijme předem nakonfigurované prostředí pro akci pro pevný počet hodin, když obdržíte vysoce kvalifikovaných potenciálního zákazníka do se. Další informace najdete v tématu [co je to Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Nastavení testovací verze

Pomocí následujících kroků povolte a nakonfigurujte si testovací jízdu.

### <a name="to-enable-a-test-drive"></a>Pokud chcete povolit testovací verze:

1. V části **nová nabídka**, vyberte **Test Drive** kartu.
2. V části **Test Drive**vyberte **Ano** pro **povolit si testovací jízdu**.

   ![Povolit testovací verze](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Konfigurace testovací verze:

Povolíte-li si testovací jízdu, budete vyplňte následujících forem nastavit testovací verze:
  
 - Podrobnosti
 - Technické konfigurace
 - Podrobnosti předplatného nasazení testu jednotky

Následující snímek obrazovky ukazuje všechny testovací verze formuláře. Hvězdičku (*) připojeným k názvu pole označuje, že je to požadováno. 

![Konfigurace testovací verze](./media/managed-app-configure-testdrive.png)

Následující tabulka popisuje pole k nastavení testů jednotky pro spravované aplikace.

|    **Pole**       |  **Popis**  |
|  ---------------   |  ---------------  |
|      Popis              |   Popište, co se dá dělat na vaše testovací verze. Základní značky HTML můžete použít k formátování tento popis. Například &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;a záhlaví.                |
|    Uživatelská příručka                |     Nahrajte uživatelská příručka pro vaši zákazníci můžou použít pro vás provedl možnostmi, testovací verze. Tento dokument musí být soubor PDF.              |
|         Test jednotky – ukázkové Video (volitelné)           |       Můžete zadat video s návodem, vaše testovací verze. Zákazník může sledovat toto video předtím, než si testovací jízdu. Zadejte URL adresu videa na YouTube nebo Vimeo. Pokud vyberete **+ přidat Video**, budete vyzváni k zadání následujících informací:<ul><li>Název</li><li>zprostředkovatele identity</li><li>Miniatura (ve formátu PNG, 533 x 324 pixelů)</li></ul>            |
|       Instance             |        Kolik instancí, které chcete nakonfigurovat, v jaké oblasti a jak rychle vaši zákazníci mohou získat testovací verze. Další informace najdete v tématu [publikování si testovací jízdu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
|       Jednotka doba trvání testu (hodiny)             |       Zadejte celé číslo pro počet hodin. Povolený rozsah je od 1 do 999.            |
|        Šablona ARM testu jednotky            |        Nahrání komprimovaný soubor (ZIP), který má své šablony Azure Resource Manageru pro vaši aplikaci. Další informace najdete v tématu [Azure Resource Manageru Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/azure-resource-manager-test-drive).            |
|        Přístup k informacím            |         Poskytuje přístup k informacím po získá váš zákazník testovací verze. Například adresa URL pro přístup k testovací verze a informace o přihlášení. . Základní značky HTML můžete použít k formátování tento popis. Například &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;a záhlaví.          |
|       Id předplatného přístup             |       Tím získají přístup ke službám Azure a webu Azure portal. Předplatné je používání prostředků vykazováno, kde služby se účtují. Pokud ještě nemáte samostatné předplatné Azure pro Test jednotek pouze, vytvořte předplatné.             |
|          Id klienta Azure AD          |        Zadejte existujícího Tenanta služby Azure Active Directory, nebo vytvořit tenanta pro tento testovací verze.           |
|         Id aplikace Azure AD           |       Vytvořte a zaregistrujte novou aplikaci. Společnost Microsoft používá k provádění operací ve vaší instanci testovací verze této aplikace.            |
|          Klíč aplikace Azure AD          |         Vytvoření ověřovací klíč pro aplikaci a vložte ho do tohoto pole.          |

Po zadání všech požadovaných informací, vyberte **Uložit** abychom mohli dokončit nastavování testovací verze.

## <a name="next-steps"></a>Další postup

[Karta Marketplace](./cpp-marketplace-tab.md)