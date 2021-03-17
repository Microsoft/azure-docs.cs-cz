---
title: Zálohování a obnovení-Azure PowerShell-Azure Database for PostgreSQL
description: Naučte se zálohovat a obnovovat Server v Azure Database for PostgreSQL pomocí Azure PowerShell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63fffb5998b0b6a245db3f1c8fcf16f2d576936e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489757"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-postgresql-server-using-powershell"></a>Postup zálohování a obnovení serveru Azure Database for PostgreSQL pomocí prostředí PowerShell

Azure Database for PostgreSQL servery se pravidelně zálohují, aby se povolily funkce obnovení. Pomocí této funkce můžete obnovit server a všechny jeho databáze k dřívějšímu bodu v čase na novém serveru.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto průvodce budete potřebovat:

- [Modul AZ PowerShell](/powershell/azure/install-az-ps) nainstalovaný místně nebo [Azure Cloud Shell](https://shell.azure.com/) v prohlížeči
- [Server Azure Database for PostgreSQL](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> I když je modul PowerShell AZ. PostgreSql ve verzi Preview, musíte ho nainstalovat samostatně z modulu AZ PowerShellu pomocí následujícího příkazu: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Jakmile je modul PowerShellu AZ. PostgreSql všeobecně dostupný, bude součástí budoucna k tomu, aby vydaná vydání modulu PowerShellu a nativně dostupná v rámci Azure Cloud Shell.

Pokud se rozhodnete použít prostředí PowerShell místně, připojte se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Nastavení konfigurace zálohování

Při vytváření serveru můžete vybrat možnost konfigurace serveru pro místně redundantní nebo geograficky redundantní zálohy.

> [!NOTE]
> Po vytvoření serveru se jeho druh redundance, geograficky redundantní vs místně redundantní nedá změnit.

Při vytváření serveru prostřednictvím příkazu se `New-AzPostgreSqlServer` rozhodne možnost redundance zálohování použít parametr **GeoRedundantBackup** . Pokud je **povoleno**, budou provedeny geograficky redundantní zálohy. Nebo pokud je **zakázaný**, provedou se místně redundantní zálohy.

Doba uchovávání záloh je nastavená parametrem **BackupRetentionDay** .

Další informace o nastavení těchto hodnot během vytváření serveru najdete v tématu [vytvoření serveru Azure Database for PostgreSQL pomocí prostředí PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md).

Dobu uchovávání záloh serveru lze změnit následujícím způsobem:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

Předchozí příklad změní dobu uchovávání záloh mydemoserver na 10 dní.

Doba uchovávání záloh určuje, jak daleko se obnovení k určitému bodu v čase dá načíst, protože je založené na dostupných zálohách. Obnovení k bodu v čase je popsáno dále v následující části.

## <a name="server-point-in-time-restore"></a>Obnovení k časovému okamžiku serveru

Server můžete obnovit k předchozímu bodu v čase. Obnovená data se zkopírují na nový server a stávající server zůstane beze změny. Například pokud je tabulka omylem vyřazena, můžete ji obnovit v čase pouhým odhozením. Pak můžete načíst chybějící tabulku a data z obnovené kopie serveru.

K obnovení serveru použijte `Restore-AzPostgreSqlServer` rutinu PowerShellu.

### <a name="run-the-restore-command"></a>Spuštění příkazu RESTORE

Pokud chcete server obnovit, spusťte z PowerShellu následující příklad.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Sada parametrů **PointInTimeRestore** `Restore-AzPostgreSqlServer` rutiny vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Skupina prostředků, ve které existuje zdrojový server.  |
| Name | mydemoserver-restored | Název nového serveru, který se vytvoří příkazem restore. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Vyberte bod v čase, který chcete obnovit. Tato datum a čas musí být v rámci doby uchovávání záloh zdrojového serveru. Použijte formát data a času ISO8601. Můžete například použít vlastní místní časové pásmo, například **2020-03-13T05:59:00-08:00**. Můžete také použít formát Zulu UTC, například **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | K obnovení použijte režim bodu v čase. |

Když server obnovíte do dřívějšího bodu v čase, vytvoří se nový server. Původní server a jeho databáze ze zadaného časového okamžiku se zkopírují na nový server.

Hodnoty umístění a cenové úrovně obnoveného serveru zůstanou stejné jako u původního serveru.

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době spuštění obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla musí být nastavená samostatně pro nový server. Obnoví se pravidla brány firewall z původního serveru.

## <a name="geo-restore"></a>Geografické obnovení

Pokud jste server nakonfigurovali pro geograficky redundantní zálohy, můžete vytvořit nový server ze zálohy stávajícího serveru. Tento nový server se dá vytvořit v libovolné oblasti, kterou Azure Database for PostgreSQL k dispozici.

Pokud chcete vytvořit server pomocí geograficky redundantní zálohy, použijte `Restore-AzPostgreSqlServer` příkaz s parametrem **UseGeoRestore** .

> [!NOTE]
> Při prvním vytvoření serveru nemusí být pro geografickou obnovu k dispozici okamžitě. Naplnění potřebných metadat může trvat několik hodin.

Pokud chcete server geograficky obnovit, spusťte z PowerShellu následující příklad:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Tento příklad vytvoří nový server s názvem **mydemoserver-geograficky obnovený** v oblasti východní USA, která patří k **myresourcegroup**. Je to Pro obecné účelyý Server Gen 5 s 8 virtuální jádra. Server se vytvoří z geograficky redundantní zálohy **mydemoserver**, a to i v **myresourcegroup**skupiny prostředků.

Pokud chcete vytvořit nový server v jiné skupině prostředků z existujícího serveru, zadejte nový název skupiny prostředků pomocí parametru **ResourceGroupName** , jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Sada **GeoRestore** parametrů pro dopředné obnovení `Restore-AzPostgreSqlServer` rutiny vyžaduje následující parametry:

| Nastavení | Navrhovaná hodnota | Popis  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Název skupiny prostředků, do které nový server patří|
|Name | mydemoserver – geograficky obnovené | Název nového serveru. |
|Umístění | eastus | Umístění nového serveru. |
|UseGeoRestore | `<SwitchParameter>` | K obnovení použijte geografický režim. |

Když vytváříte nový server pomocí geografického obnovení, zdědí stejnou velikost úložiště a cenovou úroveň jako zdrojový server, pokud není zadaný parametr **SKU** .

Po dokončení procesu obnovení Najděte nový server a ověřte, že se data obnovila podle očekávání. Nový server má stejné přihlašovací jméno a heslo správce serveru, které bylo platné pro existující server v době spuštění obnovení. Heslo lze změnit na stránce **Přehled** nového serveru.

Nový server vytvořený během obnovy nemá koncové body služby virtuální sítě, které existovaly na původním serveru. Tato pravidla musí být nastavená samostatně pro tento nový server. Obnoví se pravidla brány firewall z původního serveru.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak vygenerovat připojovací řetězec Azure Database for PostgreSQL pomocí PowerShellu](howto-connection-string-powershell.md)