---
title: Šablona privátního koncového bodu Azure
description: Informace o privátním propojení Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: af00119f1da3368b8592e020eee1ebb2a39a8501
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669949"
---
# <a name="quickstart-create-a-private-endpoint---resource-manager-template"></a>Rychlý Start: Vytvoření šablony privátního koncového bodu – Správce prostředků

V tomto rychlém startu použijete šablonu Správce prostředků k vytvoření privátního koncového bodu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý Start můžete také dokončit pomocí [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](create-private-endpoint-cli.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-private-endpoint"></a>Vytvoření privátního koncového bodu

Tato šablona vytvoří privátní koncový bod pro Azure SQL Server.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. SQL/servery**](/azure/templates/microsoft.sql/servers) : Azure SQL Server s ukázkovou databází
- [**Microsoft. SQL/servery/databáze**](/azure/templates/microsoft.sql/servers/databases) : Ukázková databáze
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Virtual Network při nasazení privátního koncového bodu
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : privátní koncový bod pro přístup k privátnímu serveru Azure SQL Server
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : používá se k překladu IP adresy privátního koncového bodu.
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : pro přidružení privátního koncového bodu k privátní zóně DNS
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses) : veřejná IP adresa pro přístup k virtuálnímu počítači
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : síťové rozhraní pro virtuální počítač
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : virtuální počítač pro otestování privátního připojení s privátním koncovým bodem na Azure SQL Server

### <a name="deploy-the-template"></a>Nasazení šablony

Nasadit šablonu Správce prostředků do Azure:

1. Vyberte **nasadit do Azure** a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří privátní koncový bod, Azure SQL Server, síťovou infrastrukturu a virtuální počítače, které se mají ověřit.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků,
3. Zadejte přihlašovací jméno a heslo správce SQL.
4. Zadejte uživatelské jméno a heslo správce virtuálního počítače.
5. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a pak vyberte **koupit**. Dokončení nasazení může trvat až 20 minut nebo i déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

> [!NOTE]
> Šablona ARM vygeneruje jedinečný název pro prostředek myVm<b>{UniqueID}</b> a pro prostředek Azure SQL Server SQLServer<b>{UniqueID}</b> , nahraďte <b>{UniqueID}</b> svou generovanou hodnotou.

### <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači _myVm {UniqueID}_ z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte _myVm {UniqueID}_.

2. Klikněte na tlačítko **Připojit**. Po výběru tlačítka **připojit** se **připojte k virtuálnímu počítači** .

3. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (_. RDP_) a stáhne ho do vašeho počítače.

4. Otevřete stažený soubor. RDP \* .

   a. Pokud se zobrazí výzva, vyberte **Připojit**.

   b. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

      > [!NOTE]
      > Možná budete muset vybrat **Další volby**  >  **použít jiný účet**a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

5. Vyberte **OK**.

6. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se zobrazí upozornění certifikátu, vyberte **Ano** nebo **pokračovat**.

7. Jakmile se zobrazí plocha virtuálního počítače, minimalizujte ji tak, aby se vrátila k místnímu počítači.

### <a name="access-sql-database-server-privately-from-the-vm"></a>Přístup k serveru SQL Database soukromě z virtuálního počítače

V této části se připojíte k serveru SQL Database z virtuálního počítače pomocí privátního koncového bodu.

1.  Ve vzdálené ploše _myVM {UniqueID}_ otevřete PowerShell.
2.  Zadejte příkaz nslookup SQLServer {UniqueID}. Database. Windows. NET   obdržíte zprávu podobnou této:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Nainstalovat SQL Server Management Studio
4.  V připojení k serveru zadejte nebo vyberte tyto informace: typ serveru: vyberte možnost databázový stroj.
    Název serveru: vyberte SQLServer {UniqueID}. Database. Windows. NET username: zadejte uživatelské jméno, které jste zadali při vytváření.
    Heslo: zadejte heslo, které jste zadali při vytváření.
    Pamatovat heslo: vyberte Ano.

5.  Vyberte **Připojit**.
6.  Procházet **databáze** z levé nabídky
7.  Volitelně Vytvoření nebo dotazování informací z _ukázky – DB_
8.  Zavřete připojení ke vzdálené ploše na _myVm {UniqueID}_.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci privátního koncového bodu, odstraňte skupinu prostředků. Tím se odstraní soukromý koncový bod a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

- Další informace o [privátním propojení Azure](private-link-overview.md)
