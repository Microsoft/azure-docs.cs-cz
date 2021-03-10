---
title: Rychlý Start – vytvoření privátního koncového bodu pomocí šablony ARM
description: V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření privátního koncového bodu.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: e80adc16e08e676ac2daabec01a11c10d537c547
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562941"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>Rychlý Start: Vytvoření privátního koncového bodu pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření privátního koncového bodu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento rychlý Start můžete také dokončit pomocí [Azure Portal](create-private-endpoint-portal.md), [Azure PowerShell](create-private-endpoint-powershell.md)nebo rozhraní příkazového [řádku Azure](create-private-endpoint-cli.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Potřebujete účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Kontrola šablony

Tato šablona vytvoří privátní koncový bod pro instanci Azure SQL Database.

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/).

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json":::

V šabloně je definováno víc prostředků Azure:

- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers): instance SQL Database s ukázkovou databází.
- [**Microsoft. SQL/servery/databáze**](/azure/templates/microsoft.sql/servers/databases): Ukázková databáze.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks): virtuální síť, ve které je nasazený privátní koncový bod.
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints): soukromý koncový bod pro přístup k instanci SQL Database.
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones): zóna používaná k překladu IP adresy privátního koncového bodu.
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups): Skupina zón používaná k přidružení privátního koncového bodu k privátní zóně DNS.
- [**Microsoft. Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses): veřejná IP adresa použitá pro přístup k virtuálnímu počítači.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces): síťové rozhraní pro virtuální počítač.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines): virtuální počítač, který slouží k otestování soukromého připojení s privátním koncovým bodem, do instance SQL Database.

## <a name="deploy-the-template"></a>Nasazení šablony

Tady je postup, jak nasadit šablonu ARM do Azure:

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte **nasadit do Azure**. Šablona vytvoří privátní koncový bod, instanci SQL Database, síťovou infrastrukturu a virtuální počítač, který se má ověřit.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Vyberte nebo vytvořte skupinu prostředků.
3. Zadejte přihlášení a heslo správce SQL.
4. Zadejte uživatelské jméno a heslo správce virtuálního počítače.
5. Přečtěte si prohlášení o podmínkách a ujednáních. Pokud souhlasíte, vyberte Souhlasím **s podmínkami a ujednáními uvedenými nad**  >  **nákupem**. Dokončení nasazení může trvat až 20 minut nebo i déle.

## <a name="validate-the-deployment"></a>Ověření nasazení

> [!NOTE]
> Šablona ARM vygeneruje jedinečný název pro prostředek myVm<b>{UniqueID}</b> virtuálního počítače a pro prostředek SQL Database SQLServer<b>{UniqueID}</b> . Vygenerovanou hodnotu pro **{UniqueID}** nahraďte.

### <a name="connect-to-a-vm-from-the-internet"></a>Připojení k virtuálnímu počítači z internetu

Připojte se k virtuálnímu počítači _myVm {UniqueID}_ z Internetu následujícím způsobem:

1. Na panelu hledání na portálu zadejte _myVm {UniqueID}_.

2. Vyberte **Connect** (Připojit). **Připojení k virtuálnímu počítači** se otevře.

3. Vyberte **Stáhnout soubor RDP**. Azure vytvoří soubor protokol RDP (Remote Desktop Protocol) (_. RDP_) a stáhne ho do vašeho počítače.

4. Otevřete stažený soubor .rdp.

   a. Pokud se zobrazí výzva, vyberte **Připojit**.

   b. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače.

      > [!NOTE]
      > Možná budete muset vybrat **Další volby**  >  **použít jiný účet** a zadat přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

5. Vyberte **OK**.

6. Při přihlášení se může zobrazit upozornění na certifikát. Pokud se zobrazí upozornění na certifikát, vyberte **Ano** nebo **Pokračovat**.

7. Po zobrazení plochy virtuálního počítače ho minimalizujte, abyste se mohli vrátit k místnímu počítači.

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>Přístup k serveru SQL Database soukromě z virtuálního počítače

Tady je postup, jak se připojit k serveru SQL Database z virtuálního počítače pomocí privátního koncového bodu.

1.  Ve vzdálené ploše _myVM {UniqueID}_ otevřete PowerShell.
2.  Zadejte následující příkaz: nslookup SQLServer {UniqueID}. Database. Windows. NET. 
    Zobrazí se zpráva podobná této:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  Nainstalujte SQL Server Management Studio.
4.  V **Connect to Server (připojit k serveru**) zadejte nebo vyberte tyto informace:
    - **Typ serveru**: vyberte možnost **databázový stroj**.
    - **Název serveru**: vyberte **SQLServer {UniqueID}. Database. Windows. NET**.
    - **Uživatelské jméno**: zadejte uživatelské jméno, které jste zadali při vytváření.
    - **Heslo**: zadejte heslo, které jste zadali při vytváření.
    - **Pamatovat heslo**: vyberte  **Ano**.

5.  Vyberte **Connect** (Připojit).
6.  V nabídce na levé straně přejděte do části **databáze**.
7.  Volitelně můžete vytvořit nebo dotazovat informace z _Sample-DB_.
8.  Zavřete připojení ke vzdálené ploše na _myVm {UniqueID}_.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky, které jste vytvořili v rámci privátního koncového bodu, odstraňte skupinu prostředků. Tím se odstraní soukromý koncový bod a všechny související prostředky.

Pokud chcete odstranit skupinu prostředků, zavolejte `Remove-AzResourceGroup` rutinu:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Další kroky

Další informace o službách, které podporují soukromý koncový bod, najdete v těchto tématech:
> [!div class="nextstepaction"]
> [Dostupnost privátního propojení](private-link-overview.md#availability)
