---
title: Použití privátních koncových bodů s účty Azure Batch
description: Naučte se připojit soukromě k účtu Azure Batch pomocí soukromých koncových bodů.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: f797dbda7888eb8ea9f5c76e3b527fb98d896ee4
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92669011"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Použití privátních koncových bodů s účty Azure Batch

Ve výchozím nastavení mají [účty Azure Batch](accounts.md) veřejný koncový bod a jsou veřejně přístupné. Služba Batch nabízí možnost vytvořit soukromé účty Batch a zakázat přístup k veřejné síti.

Pomocí [privátního propojení Azure](../private-link/private-link-overview.md)se můžete k účtu Azure Batch připojit prostřednictvím [privátního koncového bodu](../private-link/private-endpoint-overview.md). Soukromý koncový bod je sada privátních IP adres v podsíti v rámci vaší virtuální sítě. Pak můžete omezit přístup k účtu Azure Batch pomocí privátních IP adres.

Privátní odkaz umožňuje uživatelům přístup k účtu Azure Batch z virtuální sítě nebo z jakékoli partnerské virtuální sítě. Prostředky namapované na soukromé odkazy jsou k dispozici i místně přes privátní partnerský vztah prostřednictvím sítě VPN nebo [Azure ExpressRoute](../expressroute/expressroute-introduction.md). K účtu Azure Batch nakonfigurovanému pomocí privátního propojení se můžete připojit pomocí [metody automatického nebo ručního schválení](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

> [!IMPORTANT]
> Podpora pro privátní připojení v Azure Batch je aktuálně dostupná pro všechny veřejné oblasti kromě oblasti Německo – střed a Německo – severovýchod.

Tento článek popisuje kroky pro vytvoření privátního účtu Batch a přístup k němu pomocí privátního koncového bodu.

## <a name="azure-portal"></a>Azure Portal

Pomocí následujícího postupu vytvořte pomocí Azure Portal privátní účet Batch:

1. V podokně **vytvořit prostředek** zvolte **Služba Batch** a pak vyberte **vytvořit** .
2. Na kartě **základy** zadejte předplatné, skupinu prostředků, oblast a název účtu Batch a potom vyberte **Další: Upřesnit** .
3. Na kartě **Upřesnit** nastavte přístup k **veřejné síti** na **zakázaný** .
4. V **Nastavení** vyberte **připojení privátního koncového bodu** a pak vyberte **+ privátní koncový bod** .
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Připojení privátního koncového bodu":::
5. V podokně **základy** zadejte nebo vyberte předplatné, skupinu prostředků, název prostředku privátního koncového bodu a podrobnosti oblasti a potom vyberte **Další: prostředek** .
6. V podokně **prostředek** nastavte **typ prostředku** na **Microsoft.Batch/batchAccounts** . Vyberte účet privátní dávky, ke kterému chcete získat přístup, a potom vyberte **Další: Konfigurace** .
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Připojení privátního koncového bodu":::
7. V podokně **Konfigurace** zadejte nebo vyberte tyto informace:
   - **Virtuální síť** : Vyberte svou virtuální síť.
   - **Podsíť** : Vyberte svou podsíť.
   - **Integrace s privátní zónou DNS** : vyberte **Ano** . Abyste mohli soukromě propojit s vaším soukromým koncovým bodem, budete potřebovat záznam DNS. Doporučujeme integrovat privátní koncový bod s privátní zónou DNS. Můžete také použít vlastní servery DNS nebo vytvořit záznamy DNS pomocí hostitelských souborů na virtuálních počítačích.
   - **Privátní DNS zóna** : vyberte privatelink. \<region\> . batch.azure.com. Privátní zóna DNS je určena automaticky. Nemůžete ho změnit pomocí Azure Portal.
8. Vyberte **zkontrolovat + vytvořit** a potom počkejte, než Azure ověří vaši konfiguraci.
9. Jakmile se zobrazí zpráva **Ověření proběhlo úspěšně** , vyberte **Vytvořit** .

Po zřízení privátního koncového bodu můžete k účtu Batch přistupovat z virtuálních počítačů ve stejné virtuální síti pomocí privátního koncového bodu. Postup zobrazení IP adresy z Azure Portal:

1. Vyberte **Všechny prostředky** .
2. Vyhledejte privátní koncový bod, který jste vytvořili dříve.
3. Vyberte kartu **Přehled** a zobrazte tak nastavení DNS a IP adresy.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="Připojení privátního koncového bodu":::

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

Když [vytváříte účet Batch pomocí šablony Azure Resource Manager](quick-create-template.md), upravte šablonu a nastavte **publicNetworkAccess** na **disabled** , jak je uvedeno níže.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Konfigurace zón DNS

V podsíti, ve které jste vytvořili privátní koncový bod, použijte [privátní ZÓNU DNS](../dns/private-dns-privatednszone.md) . Konfigurace koncových bodů tak, aby každá privátní IP adresa byla namapovaná na položku DNS.

Při vytváření privátního koncového bodu ho můžete integrovat s [privátní zónou DNS](../dns/private-dns-privatednszone.md) v Azure. Pokud se rozhodnete místo toho použít [vlastní doménu](../dns/dns-custom-domain.md), musíte ji nakonfigurovat tak, aby přidala záznamy DNS pro všechny privátní IP adresy rezervované pro soukromý koncový bod.

## <a name="pricing"></a>Ceny

Podrobnosti o nákladech souvisejících s privátními koncovými body najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Aktuální omezení a osvědčené postupy

Při vytváření privátního účtu Batch mějte na paměti následující:

- Prostředky privátního koncového bodu se musí vytvořit ve stejném předplatném jako účet Batch.
- Chcete-li odstranit privátní připojení, je nutné odstranit prostředek privátního koncového bodu.
- Jakmile se účet Batch vytvoří s přístupem k veřejné síti, nemůžete ho změnit jenom na privátní přístup.
- Záznamy DNS v privátní zóně DNS se při odstranění privátního koncového bodu nebo při odebrání oblasti z účtu Batch automaticky neodeberou. Před přidáním nového privátního koncového bodu připojeného k této privátní zóně DNS musíte ručně odebrat záznamy DNS. Pokud neprovedete vyčištění záznamů DNS, může dojít k neočekávaným problémům roviny dat, jako jsou například výpadky dat do oblastí přidaných po odebrání privátního koncového bodu nebo odebrání oblasti.

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvářet fondy Batch ve virtuálních sítích](batch-virtual-network.md).
- Naučte se [vytvářet fondy Batch bez veřejných IP adres](batch-pool-no-public-ip-address.md) .
- Naučte se [vytvářet fondy Batch se zadanými veřejnými IP adresami](create-pool-public-ip.md).
- Přečtěte si o [privátních odkazech Azure](../private-link/private-link-overview.md).
