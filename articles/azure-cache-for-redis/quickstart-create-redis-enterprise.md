---
title: 'Rychlý Start: vytvoření mezipaměti podnikové úrovně'
description: V tomto rychlém startu se dozvíte, jak vytvořit instanci Azure cache pro úroveň Redis Enterprise.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/12/2020
ms.openlocfilehash: 3df6cb1afe8a6249eadbbd3f61619e66be2d2478
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83402495"
---
# <a name="quickstart-create-an-enterprise-tier-cache-preview"></a>Rychlý Start: vytvoření mezipaměti podnikové vrstvy (Preview)

Azure cache pro podnikové úrovně Redis poskytuje plně integrovaný a spravovaný [Redis podnik](https://redislabs.com/redis-enterprise/) v Azure. Jsou aktuálně k dispozici ve verzi Preview. V této verzi Preview jsou dvě nové úrovně:
* Enterprise, který používá pro ukládání dat nestálou paměť (DRAM) na virtuálním počítači
* Enterprise SSD, který používá stálou i nestálou paměť (NVMe) k ukládání dat.

Pro připojení ke verzi Preview se neúčtují žádné náklady. Pokud vás zajímá, zaregistrujte se [Azure Marketplace](https://aka.ms/redispreviewsignup/) **kontaktujte mě** . Máme moc velký počet bodů účastníků a nemůžeme zaručit, že se do verze Preview přijímají.

## <a name="prerequisites"></a>Předpoklady

Než začnete, budete potřebovat předplatné Azure. Pokud ho ještě nemáte, vytvořte nejdřív [bezplatný účet](https://azure.microsoft.com/free/) .

## <a name="create-a-cache"></a>Vytvoření mezipaměti
1. Pokud chcete vytvořit mezipaměť, přihlaste se k Azure Portal pomocí odkazu v rámci pozvánky ve verzi Preview a vyberte **vytvořit prostředek**.

   > [!IMPORTANT] 
   > Nevytvářejte předplatné *Azure cache pro Redis, podnikové úrovně* na webu Marketplace přímo.
   > Tento krok provádí automaticky mezipaměť Azure pro uživatelské rozhraní portálu Redis.
   >
   
1. Na stránce **Nový** vyberte **databáze** a pak vyberte **Azure cache pro Redis**.
   
   ![Vybrat mezipaměť Azure pro Redis](media/cache-create/new-cache-menu.png)
   
1. Na stránce **nový Redis Cache** nakonfigurujte nastavení pro novou mezipaměť.
   
   | Nastavení      | Navrhovaná hodnota  | Popis |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Předplatné** | Rozevírací seznam a vyberte své předplatné. | Předplatné, ve kterém se má vytvořit Tato nová mezipaměť Azure pro instanci Redis | 
   | **Skupina prostředků** | Rozevírací seznam a vyberte skupinu prostředků nebo vyberte **vytvořit novou** a zadejte nový název skupiny prostředků. | Název skupiny prostředků, ve které se má vytvořit mezipaměť a další prostředky Po uložení všech prostředků vaší aplikace do jedné skupiny prostředků je můžete snadno spravovat nebo odstraňovat společně. | 
   | **Název DNS** | Zadejte globálně jedinečný název. | Název mezipaměti musí být řetězec v rozmezí 1 až 63 znaků, který obsahuje jenom čísla, písmena nebo spojovníky. Název musí začínat a končit číslicí nebo písmenem a nesmí obsahovat po sobě jdoucí spojovníky. *Název hostitele* vaší instance mezipaměti bude * \<DNS name> . <Azure region> . redisenterprise.cache.azure.net*. | 
   | **Umístění** | Rozevírací seznam a vyberte umístění. | Úrovně Enterprise jsou k dispozici v Západní USA, Východní USA 2 a Západní Evropa. |
   | **Vrstva mezipaměti** | Vyberte rozevírací nabídku a vyberte možnost *Enterprise DRAM* nebo *Enterprise SSD* a velikost. |  Úroveň určuje velikost, výkon a funkce, které jsou k dispozici pro mezipaměť. |
   
   ![Základy na podnikové úrovni](media/cache-create/enterprise-tier-basics.png) 

1. Vyberte **Další: sítě** a přeskočit.

   > [!NOTE] 
   > Podpora privátního propojení se bude nacházet později.
   >

1. Vyberte **Další: Upřesnit**.
   
   Můžete ponechat výchozí nastavení nebo je podle potřeby změnit. Když zapnete **Povolení přístupu přes protokol TLS**, musíte k přístupu k nové mezipaměti z aplikace použít protokol TLS.

   ![Rozšířená úroveň Enterprise](media/cache-create/enterprise-tier-advanced.png) 

   > [!NOTE] 
   > Moduly Redis se zatím nepodporují v úrovni Enterprise SSD. Pokud máte v úmyslu použít modul Redis, nezapomeňte zvolit mezipaměť podnikové vrstvy.
   >
   
1. Vyberte **Další: značky** a přeskočit.

1. Vyberte **Další: Souhrn**.

   ![Souhrn úrovně Enterprise](media/cache-create/enterprise-tier-summary.png) 

1. Zaškrtněte políčko s **podmínkami**, zkontrolujte nastavení a pak vyberte **zkontrolovat + vytvořit**.
   
   Vytvoření mezipaměti trvá nějakou dobu. Průběh můžete sledovat na stránce **Přehled** služby Azure cache pro Redis. Pokud se **stav** zobrazuje jako **spuštěno**, mezipaměť je připravena k použití.

   > [!NOTE] 
   > Po vytvoření mezipaměti podnikové vrstvy obdržíte od Azure Marketplace **požadovanou akci** , která konfiguruje službu *Azure cache pro Redis, úrovně Enterprise*. Tato akce není nutná. Tento e-mail můžete bezpečně ignorovat.
   >

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit instanci podnikové vrstvy Azure cache pro Redis.

> [!div class="nextstepaction"]
> [Vytvořte webovou aplikaci ASP.NET, která používá Azure cache pro Redis.](./cache-web-app-howto.md)

