---
title: Rychlý start – konfigurace brány firewall pro server služby Analysis Services v Azure | Microsoft Docs
description: Přečtěte si, jak v Azure nakonfigurovat firewall pro instanci serveru služby Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c16a189e72edd94cf6fc60580d89dd4cfd1742e8
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443439"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Rychlý start: Konfigurace brány firewall serveru – portál

V tomto rychlém startu se dozvíte, jak nakonfigurovat firewall pro server služby Azure Analysis Services. Důležitou součástí zabezpečení serveru a jeho dat je zapnutí brány firewall a konfigurace rozsahů IP adres pro počítače, které mají přístup k serveru.

## <a name="prerequisites"></a>Požadavky

- Server služby Analysis Services v předplatném. Další informace najdete v článku [Rychlý start: Vytvoření serveru – portál](analysis-services-create-server.md) nebo v článku [Rychlý start: Vytvoření serveru – PowerShell](analysis-services-create-powershell.md).
- Jeden nebo více rozsahů IP adres pro klientské počítače (pokud jsou potřeba).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal 

[Přihlášení k portálu](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurace brány firewall

1. Když kliknete na server, otevře se stránka Přehled. 
2. V nabídce **NASTAVENÍ** > **Brána firewall** > **Povolit firewall** klikněte na **Zapnuto**.
3. Pokud chcete povolit přístup DirectQuery ze služby Power BI, klikněte u **Povolit přístup ze služby Power BI** na **Zapnuto**.  
4. (Volitelné) Zadejte jeden nebo více rozsahů IP adres. V každém rozsahu zadejte název a počáteční a koncovou IP adresu. 
5. Klikněte na **Uložit**.

     ![Nastavení brány firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete nastavení potřebovat, odstraňte rozsahy IP adres nebo vypněte bránu firewall.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili konfigurovat serverovou bránu firewall. Teď, když máte server, který je zabezpečený branou firewall, na něj můžete z portálu přidat ukázkový základní datový model. Na ukázkovém modelu se naučíte konfigurovat databázové role modelu a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkového modelu na server](analysis-services-create-sample-model.md)
