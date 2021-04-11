---
title: Vytvoření a použití vlastní domény
description: Připojte vlastní doménu k virtuálnímu počítači v Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250972"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Přidání vlastní domény do virtuálního počítače nebo prostředku Azure

V Azure existuje několik způsobů, jak připojit vlastní doménu k VIRTUÁLNÍmu počítači nebo prostředku. Pro libovolný prostředek s veřejnou IP adresou (virtuální počítač, Load Balancer, Application Gateway) je nejpřímější způsob, jak vytvořit sadu záznamů v odpovídajícím doménovém registrátoru. 

## <a name="prerequisites"></a>Požadavky 
- Potřebujete virtuální počítač s webovým serverem, na kterém běží. Pomocí [rychlého](./linux/quick-create-cli.md) startu můžete vytvořit virtuální počítač a přidat Nginx.

- Virtuální počítač musí být přístupný pro web (otevřený port 80 nebo 443). Pro bezpečnější nasazení umístěte virtuální počítač za nástroj pro vyrovnávání zatížení nebo nejdřív Application Gateway. Další informace najdete v tématu [rychlý Start: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Mít existující doménu a přístup k nastavení DNS. Další informace najdete v tématu [zakoupení vlastní domény pro Azure App Service](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Přidání vlastní domény do veřejné IP adresy virtuálního počítače

Když v Azure Portal vytvoříte virtuální počítač, automaticky se vytvoří prostředek veřejné IP adresy pro virtuální počítač. Vaše veřejná IP adresa se zobrazí na stránce s přehledem virtuálního počítače. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Zobrazuje veřejnou IP adresu v části Základy na stránce s přehledem virtuálního počítače.":::

Pokud vyberete IP adresu, zobrazí se vám další informace. Ověřte, že je **přiřazení IP adresy** nastavené na **statické**. Statická IP adresa se nemění, pokud se virtuální počítač nebo prostředek restartuje nebo vypne.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Zobrazuje konfiguraci veřejné IP adresy, abyste viděli, jestli je IP adresa statická.":::

Pokud vaše IP adresa není statická, budete muset vytvořit plně kvalifikovaný název domény. 

1. Na portálu vyberte svůj virtuální počítač. 
1. V levé nabídce vyberte **vlastnosti** .
1. V části **název veřejné IP adresy address\DNS** zadejte IP adresu.
2. V části **popisek názvu DNS** zadejte předponu, kterou chcete použít.
3. V horní části stránky vyberte **Save (Uložit** ).
4. V nabídce vlevo vyberte **Přehled** a vraťte se do okna s přehledem virtuálního počítače.
5. Ověřte, že se *název DNS* zobrazuje správně. 

Otevřete prohlížeč a zadejte IP adresu nebo plně kvalifikovaný název domény a ověřte, že se na vašem VIRTUÁLNÍm počítači zobrazuje webový obsah spuštěný.
 
Po ověření statické IP adresy nebo plně kvalifikovaného názvu domény přejděte na svého poskytovatele domény a přejděte na nastavení DNS.

Jakmile přidáte *záznam a* odkazující na veřejnou IP adresu nebo plně kvalifikovaný název domény. Například postup pro registrátora domén GoDaddy je následující:
1. Přihlaste se a vyberte vlastní doménu, kterou chcete použít.
2. V části **domény** vyberte **Spravovat vše** a pak vyberte **DNS | Správa zón**.
3. Jako **Domain Name** (Název domény) zadejte vlastní doménu a vyberte **Search** (Vyhledat).
4. Na stránce Správa DNS vyberte Přidat a vyberte v seznamu typ.
5. Vyplňte pole položky A:
    - Typ: **ponechte vybraný** .
    - Hostitel: zadejte **@**
    - Body: zadejte veřejnou IP adresu nebo plně kvalifikovaný název domény virtuálního počítače. 
    - TTL: ponechte vybranou jednu hodinu.
6. Vyberte **Uložit**.

Záznam A se přidá do tabulky záznamů DNS.
 
Po vytvoření záznamu obvykle trvá přibližně hodinu, než se služba DNS rozšíří, ale někdy může trvat až 48 hodin. 


 
## <a name="next-steps"></a>Další kroky
[Přehled ukončení protokolu TLS a koncového šifrování TLS s Application Gateway](../application-gateway/ssl-overview.md).

 
