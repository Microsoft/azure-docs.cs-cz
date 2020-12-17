---
title: Privátní koncové body
description: Pochopení procesu vytváření privátních koncových bodů pro Azure Backup a scénářů, kdy použití privátních koncových bodů pomáhá udržet zabezpečení vašich prostředků.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 3ed71e49ebc550cb7bc2041e25aa6b9bde77b1ef
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629710"
---
# <a name="private-endpoints-for-azure-backup"></a>Soukromé koncové body pro Azure Backup

Azure Backup umožňuje bezpečně zálohovat a obnovovat data z trezorů Recovery Services pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md). Soukromé koncové body používají jednu nebo více privátních IP adres z vaší virtuální sítě a efektivně donášejí službu do vaší virtuální sítě.

Tento článek vám pomůže pochopit proces vytváření privátních koncových bodů pro Azure Backup a scénáře, kdy použití privátních koncových bodů pomáhá udržet zabezpečení vašich prostředků.

## <a name="before-you-start"></a>Než začnete

- Soukromé koncové body lze vytvořit pouze pro nové trezory Recovery Services (které nemají zaregistrovány žádné položky do trezoru). Proto je nutné před pokusem o ochranu položek do trezoru vytvořit privátní koncové body.
- Jedna virtuální síť může obsahovat privátní koncové body pro několik trezorů Recovery Services. Kromě toho může mít jeden Recovery Services trezor pro něj privátní koncové body ve více virtuálních sítích. Maximální počet privátních koncových bodů, které je možné vytvořit pro trezor, je však 12.
- Po vytvoření privátního koncového bodu pro trezor se trezor zamkne. Nebude k dispozici (pro zálohování a obnovení) z sítí kromě těch, které obsahují soukromý koncový bod pro trezor. Pokud jsou všechny privátní koncové body trezoru odebrány, bude trezor přístupný ze všech sítí.
- Připojení privátního koncového bodu pro zálohování používá celkem 11 privátních IP adres ve vaší podsíti, včetně těch, které používá Azure Backup pro úložiště. Toto číslo může být pro určité oblasti Azure vyšší (až 25). Proto doporučujeme, abyste při pokusu o vytvoření privátních koncových bodů pro zálohování měli k dispozici dostatek privátních IP adres.
- I když je Recovery Services trezor používá (obojí) Azure Backup a Azure Site Recovery, Tento článek popisuje použití privátních koncových bodů jenom pro Azure Backup.
- Azure Active Directory v současné době nepodporuje privátní koncové body. Takže IP adresy a plně kvalifikované názvy domény, které Azure Active Directory pro práci v oblasti, musí mít povolený odchozí přístup ze zabezpečené sítě při provádění zálohování databází ve virtuálních počítačích Azure a při zálohování pomocí agenta MARS. Můžete také použít značky NSG a značky Azure Firewall pro povolení přístupu ke službě Azure AD (podle potřeby).
- U privátních koncových bodů se virtuální sítě se zásadami sítě nepodporují. Než budete pokračovat, budete muset zakázat zásady sítě.
- Pokud jste ho zaregistrovali před 1 2020, je nutné znovu zaregistrovat poskytovatele prostředků Recovery Services k předplatnému. Pokud chcete poskytovatele znovu zaregistrovat, přejděte do svého předplatného v Azure Portal, v levém navigačním panelu přejděte na **poskytovatel prostředků** , vyberte **Microsoft. RecoveryServices** a vyberte **znovu registrovat**.
- [Obnovení mezi oblastmi](backup-create-rs-vault.md#set-cross-region-restore) pro SQL a SAP HANA zálohy databáze není podporované, pokud má Trezor povolené privátní koncové body.

## <a name="recommended-and-supported-scenarios"></a>Doporučené a podporované scénáře

I když jsou v trezoru povolené privátní koncové body, používají se k zálohování a obnovení úloh SQL a SAP HANA jenom na virtuálních počítačích Azure a na zálohování agenta MARS. Trezor můžete použít i pro zálohování dalších úloh (nevyžadují i privátní koncové body). Kromě zálohování úloh SQL a SAP HANA a zálohování pomocí agenta MARS se také k provádění obnovení souborů pro zálohování virtuálních počítačů Azure používá privátní koncové body. Další informace najdete v následující tabulce:

| Zálohování úloh na virtuálním počítači Azure (SQL, SAP HANA), zálohování pomocí agenta MARS | Použití privátních koncových bodů se doporučuje, aby se povolilo zálohování a obnovení bez nutnosti povolení – vypíše všechny IP adresy nebo plně kvalifikované názvy domény pro Azure Backup nebo Azure Storage z vašich virtuálních sítí. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Zálohování virtuálních počítačů Azure**                                         | Zálohování virtuálního počítače nevyžaduje povolení přístupu k žádným IP adresám nebo plně kvalifikovaným názvům domén. Proto nevyžaduje privátní koncové body pro zálohování a obnovení disků.  <br><br>   Obnovení souborů z trezoru obsahujícího privátní koncové body by ale mělo být omezené na virtuální sítě, které obsahují privátní koncový bod pro trezor. <br><br>    Pokud používáte nespravované disky ACL'ed, ujistěte se, že účet úložiště, který obsahuje disky, umožňuje přístup k **důvěryhodným službám Microsoftu** , pokud je ACL'ed. |
| **Zálohování souborů Azure**                                      | Zálohy souborů Azure se ukládají v místním účtu úložiště. Proto nevyžaduje privátní koncové body pro zálohování a obnovení. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Vytváření a používání privátních koncových bodů pro zálohování

Tato část pojednává o krocích při vytváření a používání privátních koncových bodů pro Azure Backup v rámci virtuálních sítí.

>[!IMPORTANT]
> Důrazně doporučujeme postupovat podle kroků ve stejné posloupnosti, jak je uvedeno v tomto dokumentu. V takovém případě může dojít k tomu, že se vygenerování trezoru nekompatibilním s používáním privátních koncových bodů a vyžaduje restartování procesu v novém trezoru.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

V [této části](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) se dozvíte, jak vytvořit trezor pomocí klienta Azure Resource Manager. Tím se vytvoří trezor s spravovanou identitou, který už je povolený. Další informace o úložištích Recovery Services [najdete tady](./backup-azure-recovery-services-vault-overview.md).

## <a name="enable-managed-identity-for-your-vault"></a>Povolit spravovanou identitu pro svůj trezor

Spravované identity umožňují trezoru vytvářet a používat soukromé koncové body. Tato část pojednává o povolení spravované identity vašeho trezoru.

1. Přejít na Recovery Services trezor – > **identitu**.

    ![Změnit stav identity na zapnuto](./media/private-endpoints/identity-status-on.png)

1. Změňte **stav** na **zapnuto** a vyberte **Uložit**.

1. Generuje se **ID objektu** , což je spravovaná identita trezoru.

    >[!NOTE]
    >Po povolení nesmí být spravovaná identita zakázaná **(ještě dočasně** ). Zakázání spravované identity může vést k nekonzistentnímu chování.

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Udělení oprávnění k trezoru pro vytvoření požadovaných privátních koncových bodů

Pro vytvoření požadovaných privátních koncových bodů pro Azure Backup musí mít trezor (spravovaná identita trezoru) oprávnění pro následující skupiny prostředků:

- Skupina prostředků, která obsahuje cílovou virtuální síť
- Skupina prostředků, ve které se mají vytvořit privátní koncové body
- Skupina prostředků, která obsahuje zóny Privátní DNS, jak je popsáno [v podrobnostech](#creating-private-endpoints-for-backup)

Doporučujeme, abyste roli **přispěvatele** nadělili pro tyto tři skupiny prostředků do trezoru (spravovaná identita). Následující postup popisuje, jak to provést pro konkrétní skupinu prostředků (je potřeba provést u každé ze tří skupin prostředků):

1. Přejděte do skupiny prostředků a na levém panelu přejděte na **Access Control (IAM)** .
1. V **Access Control** klikněte na **Přidat přiřazení role**.

    ![Přidat přiřazení role](./media/private-endpoints/add-role-assignment.png)

1. V podokně **Přidat přiřazení role** vyberte jako **roli** možnost **Přispěvatel** a jako **objekt zabezpečení** použijte **název** trezoru. Vyberte svůj trezor a po dokončení vyberte **Uložit** .

    ![Výběr role a objektu zabezpečení](./media/private-endpoints/choose-role-and-principal.png)

Pokud chcete spravovat oprávnění na podrobnější úrovni, přečtěte si téma [Vytvoření rolí a oprávnění ručně](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Vytváření a schvalování privátních koncových bodů pro Azure Backup

### <a name="creating-private-endpoints-for-backup"></a>Vytváření privátních koncových bodů pro zálohování

Tato část popisuje proces vytvoření privátního koncového bodu pro váš trezor.

1. Na panelu hledání vyhledejte a vyberte **privátní odkaz**. Tím přejdete na **centrum privátních odkazů**.

    ![Vyhledat privátní odkaz](./media/private-endpoints/search-for-private-link.png)

1. V levém navigačním panelu vyberte **soukromé koncové body**. V podokně **privátní koncové body** vyberte **+ Přidat** a začněte vytvářet privátní koncový bod pro svůj trezor.

    ![Přidat privátní koncový bod do centra privátních odkazů](./media/private-endpoints/add-private-endpoint.png)

1. Jednou v procesu **Vytvoření privátního koncového bodu** budete muset zadat podrobnosti pro vytvoření připojení privátního koncového bodu.

    1. **Základy**: Vyplňte základní podrobnosti vašich privátních koncových bodů. Oblast by měla být stejná jako u trezoru a prostředku.

        ![Vyplnit základní podrobnosti](./media/private-endpoints/basic-details.png)

    1. **Prostředek**: Tato karta vyžaduje, abyste uváděli prostředek PaaS, pro který chcete vytvořit připojení. Pro požadované předplatné vyberte **Microsoft. RecoveryServices/trezory** z typu prostředku. Po dokončení vyberte název vašeho trezoru Recovery Services jako **prostředek** a **AzureBackup** jako **cílový dílčí prostředek**.

        ![Vyplnit kartu prostředku](./media/private-endpoints/resource-tab.png)

    1. **Konfigurace**: v části Konfigurace zadejte virtuální síť a podsíť, ve které chcete vytvořit privátní koncový bod. Toto bude virtuální síť, ve které se virtuální počítač nachází. Můžete se rozhodnout pro **integraci privátního koncového bodu** s privátní zónou DNS. Alternativně můžete použít také vlastní server DNS nebo vytvořit privátní zónu DNS.

        ![Vyplnit kartu Konfigurace](./media/private-endpoints/configuration-tab.png)

        V [této části](#dns-changes-for-custom-dns-servers) najdete informace o tom, jestli chcete používat vlastní servery DNS místo integrace se službou Azure privátní DNS Zones.  

    1. Volitelně můžete přidat **značky** pro privátní koncový bod.

    1. Až se dokončí zadání podrobností, pokračujte v **kontrole a vytváření** . Po dokončení ověření vyberte **vytvořit** a vytvořte tak privátní koncový bod.

## <a name="approving-private-endpoints"></a>Schvalování privátních koncových bodů

Pokud uživatel vytvářející soukromý koncový bod je zároveň vlastníkem Recovery Services trezoru, pak se privátní koncový bod, který jste vytvořili výše, automaticky schválí. V opačném případě musí vlastník trezoru před jeho použitím schválit privátní koncový bod. Tato část popisuje ruční schválení privátních koncových bodů prostřednictvím Azure Portal.

V tématu [ruční schválení privátních koncových bodů pomocí klienta Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) použijte klienta Azure Resource Manager pro schvalování privátních koncových bodů.

1. V úložišti Recovery Services přejděte na levý panel na **připojení privátního koncového bodu** .
1. Vyberte připojení privátního koncového bodu, které chcete schválit.
1. V horním panelu vyberte **schválit** . Můžete také vybrat možnost **odmítnout** nebo **Odebrat** , pokud chcete odmítat nebo odstranit připojení koncového bodu.

    ![Schválení privátních koncových bodů](./media/private-endpoints/approve-private-endpoints.png)

## <a name="using-private-endpoints-for-backup"></a>Použití privátních koncových bodů pro zálohování

Po schválení privátních koncových bodů vytvořených pro trezor ve vaší virtuální síti je můžete začít používat pro zálohování a obnovení.

>[!IMPORTANT]
>Než budete pokračovat, ujistěte se, že jste úspěšně dokončili všechny kroky uvedené výše v dokumentu. K rekapitulace je nutné provést kroky v následujícím kontrolním seznamu:
>
>1. Vytvořil se (nový) Recovery Services trezor.
>1. Povolit trezor pro použití spravované identity přiřazené systémem
>1. Přiřazená příslušná oprávnění k spravované identitě trezoru
>1. Vytvořili jste privátní koncový bod pro svůj trezor.
>1. Schválen privátní koncový bod (Pokud není automaticky schválen)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Zálohování a obnovení úloh na virtuálním počítači Azure (SQL, SAP HANA)

Po vytvoření a schválení privátního koncového bodu se na straně klienta nevyžadují žádné další změny pro použití privátního koncového bodu. Veškerá komunikace a přenos dat ze zabezpečené sítě do trezoru se provede prostřednictvím privátního koncového bodu.
Pokud ale po registraci serveru (SQL/SAP HANA) odeberete privátní koncové body pro trezor, budete muset kontejner znovu zaregistrovat do trezoru. Nemusíte pro ně zastavovat ochranu.

### <a name="backup-and-restore-through-mars-agent"></a>Zálohování a obnovení prostřednictvím agenta MARS

Při použití agenta MARS k zálohování místních prostředků se ujistěte, že vaše místní síť (obsahující vaše prostředky, které se mají zálohovat) má partnerský vztah s virtuální sítí Azure, která obsahuje privátní koncový bod pro trezor, takže ho můžete použít. Pak můžete pokračovat v instalaci agenta MARS a nakonfigurovat zálohování tak, jak je popsáno zde. Je však třeba zajistit, aby veškerá komunikace pro zálohování procházela pouze prostřednictvím partnerské sítě.

Pokud však po registraci agenta MARS dojde k odebrání privátních koncových bodů pro trezor, bude nutné kontejner znovu zaregistrovat do trezoru. Nemusíte pro ně zastavovat ochranu.

## <a name="additional-topics"></a>Další témata

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Vytvoření trezoru Recovery Services pomocí klienta Azure Resource Manager

Můžete vytvořit Recovery Services trezor a povolit jeho spravovanou identitu (vyžaduje se spravovaná identita, jak ho později ukážeme) pomocí klienta Azure Resource Manager. Ukázka pro tuto akci je sdílená níže:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

Výše uvedený soubor JSON by měl mít následující obsah:

JSON žádosti:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

Odpověď JSON:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>Trezor vytvořený v tomto příkladu prostřednictvím klienta Azure Resource Manager je již vytvořen pomocí spravované identity přiřazené systémem.

### <a name="managing-permissions-on-resource-groups"></a>Správa oprávnění pro skupiny prostředků

Spravovaná identita úložiště musí mít ve skupině prostředků a ve virtuální síti, kde se vytvoří privátní koncové body, následující oprávnění:

- `Microsoft.Network/privateEndpoints/*` Tato operace je nutná k provedení operace CRUD u privátních koncových bodů ve skupině prostředků. Měl by se přiřadit ke skupině prostředků.
- `Microsoft.Network/virtualNetworks/subnets/join/action` To je vyžadováno ve virtuální síti, ve které se privátní IP adresa připojuje k privátnímu koncovému bodu.
- `Microsoft.Network/networkInterfaces/read` To je vyžadováno ve skupině prostředků, aby bylo možné získat síťové rozhraní vytvořené pro soukromý koncový bod.
- Role přispěvatele Privátní DNS zóny už tato role existuje a dá se použít k poskytnutí `Microsoft.Network/privateDnsZones/A/*` a `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` oprávnění.

K vytvoření rolí s požadovanými oprávněními můžete použít jednu z následujících metod:

#### <a name="create-roles-and-permissions-manually"></a>Ruční vytvoření rolí a oprávnění

Vytvořte následující soubory JSON a pomocí příkazu PowerShellu na konci oddílu vytvořte role:

PrivateEndpointContributorRoleDef.jsna

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsna

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsna

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Použití skriptu

1. Spusťte **Cloud Shell** v Azure Portal a v okně PowerShellu vyberte **nahrát soubor** .

    ![Vybrat odeslat soubor v okně PowerShellu](./media/private-endpoints/upload-file-in-powershell.png)

1. Nahrajte tento skript: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Přejít do domovské složky (například: `cd /home/user` )

1. Spusťte tento skript:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Tyto parametry jsou:

    - **předplatné**: * * SubscriptionId, které má skupinu prostředků, ve které se vytvoří privátní koncový bod pro trezor, a podsíť, ve které se připojí privátní koncový bod trezoru.

    - **vaultPEResourceGroup**: Skupina prostředků, ve které se vytvoří privátní koncový bod úložiště.

    - **vaultPESubnetResourceGroup**: Skupina prostředků podsítě, ke které se připojí privátní koncový bod

    - **vaultMsiName**: název souboru MSI trezoru, který je stejný jako název **trezoru** .

1. Dokončete ověřování a skript převezme kontext daného předplatného, které jste zadali výše. Vytvoří příslušné role, pokud v tenantovi chybí, a přiřadí role ke službě MSI tohoto trezoru.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Vytváření privátních koncových bodů pomocí Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Automaticky schválené privátní koncové body

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Ruční schválení privátních koncových bodů pomocí klienta Azure Resource Manager

1. Pomocí **Gettrezoru** Získejte ID připojení privátního koncového bodu pro váš soukromý koncový bod.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Tím se vrátí ID připojení privátního koncového bodu. Název připojení se dá načíst pomocí první části ID připojení, a to následujícím způsobem:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Získejte **ID připojení privátního koncového bodu** (a **název privátního koncového bodu** bez ohledu na to, kde je potřeba), a nahraďte ho v následujících JSON a Azure Resource Manager URI a zkuste změnit stav na "schváleno/odmítnuto/odpojeno", jak je znázorněno v následující ukázce:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>Změny DNS pro vlastní servery DNS

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Vytváření zón DNS pro vlastní servery DNS

Musíte vytvořit tři privátní zóny DNS a propojit je s vaší virtuální sítí.

| **Zóna**                                                     | **Služba** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Objekt blob        |
| `privatelink.queue.core.windows.net`                           | Fronta       |

>[!NOTE]
>V textu výše odkazuje *zeměpis* na kód oblasti. Například *wcus* a *ne* pro středozápadní USA a Severní Evropa v uvedeném pořadí.

Kódy oblastí najdete v [tomto seznamu](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) . Pokyny pro pojmenování adres URL v národních oblastech najdete na následujících odkazech:

- [Čína](/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Německo](../germany/germany-developer-guide.md#endpoint-mapping)
- [US Gov](../azure-government/documentation-government-developer-guide.md)

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Přidávání záznamů DNS pro vlastní servery DNS

To vyžaduje, abyste v rámci vašeho privátního koncového bodu v zóně Privátní DNS provedli záznamy pro každý plně kvalifikovaný název domény.

Je potřeba poznamenat, že budeme používat privátní koncové body vytvořené pro zálohování, BLOB a Služba front.

- Privátní koncový bod pro trezor používá název zadaný při vytváření privátního koncového bodu.
- U privátních koncových bodů pro objekty BLOB a front jsou předpony s názvem stejnými pro trezor.

Následující obrázek například ukazuje tři privátní koncové body vytvořené pro připojení privátního koncového bodu s názvem *pee2epe*:

![Tři privátní koncové body pro připojení privátního koncového bodu](./media/private-endpoints/three-private-endpoints.png)

Zóna DNS pro službu zálohování ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Přejděte do privátního koncového bodu pro zálohování v **centru privátních odkazů**. Na stránce Přehled je uveden seznam plně kvalifikovaného názvu domény a privátních IP adres pro váš soukromý koncový bod.

1. Přidejte jednu položku pro každý plně kvalifikovaný název domény a soukromou IP adresu jako záznam typu.

    ![Přidat položku pro každý plně kvalifikovaný název domény a privátní IP adresu](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Zóna DNS pro Blob service ( `privatelink.blob.core.windows.net` ):

1. Přejděte na soukromý koncový bod pro objekt BLOB v **centru privátních odkazů**. Na stránce Přehled je uveden seznam plně kvalifikovaného názvu domény a privátních IP adres pro váš soukromý koncový bod.

1. Přidejte položku pro plně kvalifikovaný název domény a soukromou IP adresu jako záznam typu.

    ![Přidat položku pro plně kvalifikovaný název domény a privátní IP adresu jako záznam typu pro Blob service](./media/private-endpoints/add-type-a-record-for-blob.png)

Zóna DNS pro Služba front ( `privatelink.queue.core.windows.net` ):

1. Přejděte na soukromý koncový bod pro frontu v **centru privátních odkazů**. Na stránce Přehled je uveden seznam plně kvalifikovaného názvu domény a privátních IP adres pro váš soukromý koncový bod.

1. Přidejte položku pro plně kvalifikovaný název domény a soukromou IP adresu jako záznam typu.

    ![Přidat položku pro plně kvalifikovaný název domény a privátní IP adresu jako záznam typu pro Služba front](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Otázka: Můžu vytvořit privátní koncový bod pro existující trezor služby Backup?<br>
A. Ne, privátní koncové body lze vytvořit pouze pro nové trezory služby Backup. Takže trezor nesmí mít v minulosti žádné chráněné položky. Před vytvořením privátních koncových bodů se ale nedají provést žádné pokusy o ochranu položek do trezoru.

Otázka: Pokusili jste se chránit položku do trezoru, ale nedošlo k jejímu uložení a trezor neobsahuje žádné chráněné položky. Můžu pro tento trezor vytvořit privátní koncové body?<br>
A. Ne, trezor nesmí mít žádné pokusy o ochranu jakýchkoli položek v minulosti.

Otázka: Mám trezor, který používá privátní koncové body pro zálohování a obnovení. Můžu později přidat nebo odebrat soukromé koncové body pro tento trezor i v případě, že jsou k němu chráněné zálohované položky?<br>
A. Yes. Pokud jste již vytvořili privátní koncové body pro trezor a chráněné zálohované položky, můžete později přidat nebo odebrat soukromé koncové body podle potřeby.

Otázka: Může být privátní koncový bod pro Azure Backup také použit pro Azure Site Recovery?<br>
A. Ne, privátní koncový bod pro zálohování se dá použít jenom pro Azure Backup. Pro Azure Site Recovery budete muset vytvořit nový privátní koncový bod, pokud ho služba podporuje.

Otázka: Vynechali jsme některý z kroků v tomto článku a zavedli jsme k ochraně zdroje dat. Můžu pořád používat privátní koncové body?<br>
A. Není nutné postupovat podle kroků uvedených v článku a nadále chránit položky může způsobit, že trezor nebude moci používat privátní koncové body. Proto doporučujeme, abyste před pokračováním v ochraně položek přeodkazovali na tento kontrolní seznam.

Otázka: Můžu použít vlastní server DNS místo používání privátní zóny DNS Azure nebo integrované privátní zóny DNS?<br>
A. Ano, můžete použít vlastní servery DNS. Zajistěte však, aby byly do této části přidány všechny požadované záznamy DNS jako navržené.

Otázka: Potřebuji na svém serveru provést další kroky, I když jsem použil postup v tomto článku?<br>
A. Po provedení postupu popsaného v tomto článku není nutné provádět další práci, aby bylo možné použít privátní koncové body pro zálohování a obnovení.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o všech [funkcích zabezpečení v Azure Backup](security-overview.md)
