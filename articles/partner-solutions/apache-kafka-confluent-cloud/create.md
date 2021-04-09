---
title: Vytvoření Apache Kafka pro cloudová řešení Azure Partnerská řešení
description: Tento článek popisuje, jak pomocí Azure Portal vytvořit instanci Apache Kafka pro Cloud.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253763"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Rychlý Start: Začínáme s Apache Kafka pro Cloud s nefluenti

V tomto rychlém startu použijete Azure Portal k vytvoření instance Apache Kafka pro Cloud.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud nemáte aktivní předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
- Musíte mít roli _vlastníka_ nebo _přispěvatele_ pro vaše předplatné Azure. Integraci mezi Azure a influent může nastavit jenom uživatelé s přístupem _vlastníka_ nebo _přispěvatele_ . Než začnete, [Ověřte, že máte odpovídající přístup](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Najít nabídku

K vyhledání Apache Kafka pro cloudovou aplikaci pro influent použijte Azure Portal.

1. Ve webovém prohlížeči přejdete na [Azure Portal](https://portal.azure.com/) a přihlaste se.

1. Pokud jste **Marketplace** navštívili v nedávné relaci, vyberte ikonu z dostupných možností. V opačném případě vyhledejte _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Ikona Marketplace":::

1. Na stránce **Marketplace** máte dvě možnosti založené na typu plánu, který chcete. Můžete si zaregistrovat plán průběžných plateb nebo plán závazku. Průběžné platby jsou veřejně dostupné. Plán závazku je dostupný pro zákazníky, kteří byli schváleni pro soukromou nabídku.

   - Pro zákazníky s **průběžnými platbami můžete** hledat _Apache Kafka v cloudu_ s průběžnými platbami. Vyberte nabídku Apache Kafka v cloudu s nefluent.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="hledání Azure Marketplace nabídky":::

   - Pro zákazníky se **závazky** vyberte odkaz pro **zobrazení soukromých nabídek**. Závazek vyžaduje, abyste se přihlásili k minimálnímu množství útraty. Tuto možnost použijte jenom v případě, že víte, že službu potřebujete delší dobu.

     :::image type="content" source="media/view-private-offers.png" alt-text="Zobrazit soukromé nabídky.":::

     Hledání _Apache Kafka v cloudu s nefluent_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Vyberte soukromá nabídka.":::

## <a name="create-resource"></a>Vytvoření prostředku

Po výběru nabídky pro Apache Kafka v cloudu s využitím cloudu jste připraveni nastavit aplikaci.

1. Pokud jste v předchozí části vybrali možnost soukromé nabídky, budete mít dvě možnosti pro typy plánů:

    - Cloud s průběžnými platbami – průběžné platby
    - Plán závazku pro potvrzení

   Pokud jste nevybrali soukromé nabídky, budete mít jenom možnost platby s průběžnými platbami.

   Vyberte plán, který se má použít, a vyberte **nastavit + předplatné**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Nastavení a přihlášení k odběru.":::

1. Na stránce vytvoření základních informací o **cloudovém prostředku** zadejte následující hodnoty. Po dokončení vyberte **Další: značky**.

    :::image type="content" source="media/setup-basics.png" alt-text="Vytvořením cloudového prostředku.":::

    | Vlastnost | Popis |
    | ---- | ---- |
    | **Předplatné** | V rozevírací nabídce vyberte předplatné Azure, do kterého se má nasadit. Musíte mít přístup _vlastníka_ nebo _přispěvatele_ . |
    | **Skupina prostředků** | Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použijte existující skupinu prostředků. Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Další informace naleznete v tématu [Přehled skupin prostředků v Azure](../../azure-resource-manager/management/overview.md). |
    | **Název organizace v Fluent** | Pojmenujte prostředek software jako služba (SaaS). |
    | **Oblast** | V rozevírací nabídce vyberte jednu z těchto oblastí: <br/><br/> Austrálie – východ, Kanada – střed, Střed USA, Východní USA, Východní USA 2, Francie – střed, Severní Evropa, jihovýchodní Asie, Velká Británie – jih, Středozápadní USA, Západní Evropa, Západní USA 2 |
    | **Plán** | Vyberte **průběžné platby podle** aktuálního využití nebo **závazku**. |
    | **Fakturační období** | Vyplní se na základě vybraného plánu fakturace. |
    | **Cena** | Vyplní se na základě vybraného plánu. |

1. V části **značky** zadejte páry **název** a **hodnota** pro značky, které chcete použít pro prostředek. Po zadání značek vyberte **zkontrolovat + vytvořit**.

    :::image type="content" source="media/setup-tags.png" alt-text="Přidejte značky projektu.":::

1. Zkontrolujte zadaná nastavení. Až budete připraveni, vyberte **vytvořit**.

1. Vytvoření prostředku trvá několik minut. Stav nasazení můžete zobrazit v části **oznámení**. Po dokončení nasazení vyberte prostředek, který chcete zobrazit na stránce s **přehledem** .

    :::image type="content" source="media/deployment-status.png" alt-text="Stav nasazení.":::

   Pokud se zobrazí chyba, přečtěte si téma [řešení potíží s Apache Kafka pro cloudová řešení](troubleshoot.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa cloudového prostředku s možností influent](manage.md)
