---
title: Rychlý Start – konfigurace brány firewall serveru Azure Analysis Services | Microsoft Docs
description: Tento rychlý Start vám pomůže nakonfigurovat bránu firewall pro Azure Analysis Services Server pomocí Azure Portal.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e4953137cf939c35c6ac73fe51ca43eca6e99edc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88192445"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Rychlý start: Konfigurace brány firewall serveru – portál

V tomto rychlém startu se dozvíte, jak nakonfigurovat firewall pro server služby Azure Analysis Services. Důležitou součástí zabezpečení serveru a jeho dat je zapnutí brány firewall a konfigurace rozsahů IP adres pro počítače, které mají přístup k serveru.

## <a name="prerequisites"></a>Požadavky

- Server služby Analysis Services v předplatném. Další informace najdete v článku [Rychlý start: Vytvoření serveru – portál](analysis-services-create-server.md) nebo v článku [Rychlý start: Vytvoření serveru – PowerShell](analysis-services-create-powershell.md).
- Jeden nebo více rozsahů IP adres pro klientské počítače (pokud jsou potřeba).

> [!NOTE]
> Import dat (aktualizace) a připojení stránkovaných sestav z Power BI Premium v Microsoft Cloud Německu se v současné době nepodporují, pokud je povolená brána firewall, a to i v případě, že je nastavení povolit přístup z Power BI nastaveno na zapnuto.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal 

[Přihlásit se k portálu](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Konfigurace brány firewall

1. Když kliknete na server, otevře se stránka Přehled. 
2. V **Nastavení**  >  **Brána firewall**  >  **Povolit bránu firewall** vyberte **zapnuto**.
3. Pokud chcete povolit připojení z Power BI a Power BI Premium, v nabídce **Povolit přístup z Power BI** vyberte **zapnuto**.  
4. (Volitelné) Zadejte jeden nebo více rozsahů IP adres. V každém rozsahu zadejte název a počáteční a koncovou IP adresu. Název pravidla brány firewall by měl být omezený na 128 znaků a může obsahovat jenom velká písmena, malá písmena, číslice, podtržítka a spojovníky. Prázdné mezery a jiné speciální znaky nejsou povoleny.
5. Klikněte na **Uložit**.

     ![Nastavení brány firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až nebudete nastavení potřebovat, odstraňte rozsahy IP adres nebo vypněte bránu firewall.

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili konfigurovat serverovou bránu firewall. Teď, když máte server, který je zabezpečený branou firewall, na něj můžete z portálu přidat ukázkový základní datový model. Na ukázkovém modelu se naučíte konfigurovat databázové role modelu a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkového modelu na server](analysis-services-create-sample-model.md)
