---
title: Úvodní příručka – konfigurace brány firewall serveru Služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Tento rychlý start vám pomůže nakonfigurovat bránu firewall pro server Azure Analysis Services pomocí portálu Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 68994f9b79af55b32527eed52bbc4e5866c89538
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205161"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Rychlý start: Konfigurace brány firewall serveru – portál

V tomto rychlém startu se dozvíte, jak nakonfigurovat firewall pro server služby Azure Analysis Services. Důležitou součástí zabezpečení serveru a jeho dat je zapnutí brány firewall a konfigurace rozsahů IP adres pro počítače, které mají přístup k serveru.

## <a name="prerequisites"></a>Požadavky

- Server služby Analysis Services v předplatném. Další informace najdete v článku [Rychlý start: Vytvoření serveru – portál](analysis-services-create-server.md) nebo v článku [Rychlý start: Vytvoření serveru – PowerShell](analysis-services-create-powershell.md).
- Jeden nebo více rozsahů IP adres pro klientské počítače (pokud jsou potřeba).
- Některé scénáře, ve kterých se Power BI Premium připojuje ke službě Azure Analysis Services, včetně importu dat (aktualizace) a stránkovaných sestav, nejsou momentálně podporované, i když je povolený povolený přístup z Power BI. Běžnější scénář používání live connectu z Power BI Premium je podporovaný. Všechny scénáře Power BI Pro jsou podporované.


## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal 

[Přihlášení k portálu](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurace brány firewall

1. Když kliknete na server, otevře se stránka Přehled. 
2. V **nastavení** > **brány firewall** > **Povolit bránu firewall**klepněte na tlačítko **Zapnuto**.
3. Pokud chcete povolit přístup DirectQuery ze služby Power BI, u položky **Povolit přístup ze služby Power BI** klikněte na **Zapnuto**.  
4. (Volitelné) Zadejte jeden nebo více rozsahů IP adres. V každém rozsahu zadejte název a počáteční a koncovou IP adresu. Název pravidla brány firewall by měl být omezen na 128 znaků a může obsahovat pouze velká písmena, malá písmena, čísla, podtržítko a pomlčku. Mezery a další speciální znaky nejsou povoleny.
5. Klikněte na **Uložit**.

     ![Nastavení brány firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete nastavení potřebovat, odstraňte rozsahy IP adres nebo vypněte bránu firewall.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili konfigurovat serverovou bránu firewall. Teď, když máte server, který je zabezpečený branou firewall, na něj můžete z portálu přidat ukázkový základní datový model. Na ukázkovém modelu se naučíte konfigurovat databázové role modelu a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkového modelu na server](analysis-services-create-sample-model.md)
