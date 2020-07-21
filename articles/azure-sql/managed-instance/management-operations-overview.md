---
title: Operace správy
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o době trvání operací správy spravované instance Azure SQL a osvědčených postupech.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531190"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Přehled operací správy spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>Co jsou operace správy?
Spravovaná instance Azure SQL poskytuje operace správy, které můžete použít k automatickému nasazení nových spravovaných instancí, aktualizaci vlastností instance a odstranění instancí, pokud už nepotřebujete.

Za účelem podpory [nasazení v rámci virtuálních sítí Azure](../../virtual-network/virtual-network-for-azure-services.md) a zajištění izolace a zabezpečení pro zákazníky závisí spravovaná instance SQL na [virtuálních clusterech](connectivity-architecture-overview.md#high-level-connectivity-architecture). Virtuální cluster představuje vyhrazenou sadu izolovaných virtuálních počítačů nasazených v podsíti virtuální sítě zákazníka. Každé nasazení spravované instance v rámci prázdné podsítě má za následek nové sestavení virtuálních clusterů.

Následné operace na nasazených spravovaných instancích můžou mít vliv i na základní virtuální cluster. Tyto operace ovlivňují dobu trvání operací správy, protože nasazení dalších virtuálních počítačů přináší režii, která je potřeba vzít v úvahu při plánování nových nasazení nebo aktualizací existujících spravovaných instancí.

Všechny operace správy je možné uspořádat do následujících kategorií:

- Nasazení instance (vytvoření nové instance).
- Aktualizace instance (Změna vlastností instance, jako je virtuální jádra nebo rezervované úložiště)
- Odstranění instance.

## <a name="management-operations-duration"></a>Doba trvání operací správy
Operace s virtuálními clustery se obvykle vybírají nejdéle. Doba trvání operací na virtuálních clusterech se liší – níže jsou hodnoty, které můžete obvykle očekávat na základě stávajících dat telemetrie služby:

- **Vytvoření virtuálního clusteru**: vytváření je synchronní krok v operacích správy instancí. **90% dokončených operací za 4 hodiny**.
- **Změna velikosti virtuálního clusteru (rozšíření nebo zmenšení)**: rozšíření je synchronní krok, zatímco zmenšování se provádí asynchronně (bez dopadu na dobu trvání operací správy instancí). **90% rozšíření clusteru skončí za méně než 2,5 hodin**.
- **Odstranění virtuálního clusteru**: odstranění je asynchronní krok, ale dá se taky [iniciovat ručně](virtual-cluster-delete.md) v prázdném virtuálním clusteru. v takovém případě se to provede synchronně. **90% odstranění virtuálních clusterů se dokončí za 1,5 hodin**.

Kromě toho může Správa instancí zahrnovat taky jednu z operací na hostovaných databázích, což vede k delším dobu trvání:

- **Připojení databázových souborů z Azure Storage**: synchronní krok, například výpočetní výkon (virtuální jádra), nebo škálování úložiště v úrovni služby pro obecné účely. **90% těchto operací skončí za 5 minut**.
- **Dosazení skupiny dostupnosti Always On**: synchronní krok, například výpočetní výkon (virtuální jádra) nebo škálování úložiště v úrovni služby pro důležité obchodní informace a změna úrovně služby z Pro obecné účely na pro důležité obchodní informace (nebo naopak). Doba trvání této operace je úměrná celkové velikosti databáze a aktuální aktivitě databáze (počet aktivních transakcí). Databázová aktivita při aktualizaci instance může způsobit značnou odchylku od celkové doby trvání. **90% těchto operací se spustí v 220 GB/hod nebo vyšších**.

V následující tabulce najdete souhrn operací a typických celkových dob trvání:

|Kategorie  |Operace  |Dlouho běžící segment  |Odhadovaná doba trvání  |
|---------|---------|---------|---------|
|**Nasazení** |První instance v prázdné podsíti|Vytvoření virtuálního clusteru|90% dokončených operací za 4 hodiny.|
|Nasazení |První instance jiné generace hardwaru v neprázdné podsíti (například první instance Gen 5 v podsíti s instancemi Gen 4)|Vytváření virtuálních clusterů *|90% dokončených operací za 4 hodiny.|
|Nasazení |Vytvoření první instance 4 virtuální jádra v prázdné nebo neprázdné podsíti|Vytváření virtuálních clusterů * *|90% dokončených operací za 4 hodiny.|
|Nasazení |Vytváření dalších instancí v neprázdné podsíti (druhá, třetí atd. instance)|Změna velikosti virtuálního clusteru|90% dokončených operací za 2,5 hodin.|
|**Aktualizace** |Změna vlastnosti instance (heslo správce, přihlášení Azure AD, příznak Zvýhodněné hybridní využití Azure)|–|Až 1 minuta.|
|Aktualizace |Horizontální navýšení kapacity úložiště instance (Pro obecné účely úroveň služeb)|Připojení souborů databáze|90% dokončených operací za 5 minut.|
|Aktualizace |Horizontální navýšení kapacity úložiště instance (Pro důležité obchodní informace úroveň služeb)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času k osazení všech databází (220 GB za hodinu).|
|Aktualizace |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro obecné účely)|– Změna velikosti virtuálního clusteru<br>-Připojení souborů databáze|90% dokončených operací za 2,5 hodin.|
|Aktualizace |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro důležité obchodní informace)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času k osazení všech databází (220 GB za hodinu).|
|Aktualizace |Instance instance služby instance (Pro obecné účely až Pro důležité obchodní informace a naopak)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času k osazení všech databází (220 GB za hodinu).|
|**Odstranění**|Odstranění instance|Zálohování koncového protokolu pro všechny databáze|90% operací skončilo během až 1 minuty.<br>Poznámka: Pokud je odstraněna poslední instance v podsíti, tato operace provede odstranění virtuálního clusteru po 12 hodinách. * *|
|Odstranění|Odstranění virtuálního clusteru (jako operace iniciované uživatelem)|Odstranění virtuálního clusteru|90% operací se dokončilo během až 1,5 hodin.|

\*Virtuální cluster je postaven na generaci hardwaru.

\*\*12 hodin je aktuální konfigurace, která se může v budoucnu změnit, takže na ni nemusíte nic udělat. Pokud potřebujete virtuální cluster odstranit dřív (například pro vydání podsítě), přečtěte si téma [odstranění podsítě po odstranění spravované instance](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Dostupnost instance během operací správy

Spravovaná instance SQL **je k dispozici během operací aktualizace**, s výjimkou krátkého výpadku způsobeného převzetím služeb při selhání, ke kterému dochází na konci aktualizace. Obvykle trvá až 10 sekund i v případě přerušených dlouhotrvajících transakcí díky [urychlenému obnovení databáze](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Nedoporučujeme škálovat výpočetní výkon nebo úložiště spravované instance SQL Azure nebo změnit úroveň služby současně s dlouhotrvajícími transakcemi (import dat, úlohy zpracování dat, opětovné sestavení indexu atd.). Převzetí služeb při selhání databáze, které bude provedeno na konci operace zruší všechny probíhající transakce.

Spravovaná instance SQL není během operací nasazení a odstraňování dostupná pro klientské aplikace.

## <a name="management-operations-cross-impact"></a>Operace správy – mezi důsledky

Operace správy na spravované instanci může ovlivnit jiné operace správy instancí umístěných uvnitř stejného virtuálního clusteru:

- **Dlouhodobě běžící operace obnovení** ve virtuálním clusteru budou blokovány při vytváření jiných instancí nebo operacích škálování ve stejné podsíti.<br/>**Příklad:** Pokud existuje dlouhotrvající operace obnovení a ve stejné podsíti je žádost o vytvoření nebo škálování, bude dokončení této žádosti trvat déle, protože bude čekat na dokončení operace obnovení, než bude pokračovat.
    
- **Následná operace vytvoření nebo škálování instance** je blokována dříve zahájeným vytvořením instance nebo škálováním instance, které iniciují změnu velikosti virtuálního clusteru.<br/>**Příklad:** Pokud ve stejné podsíti v rámci stejného virtuálního clusteru existuje více požadavků na vytvoření a/nebo škálování a jedna z nich zahájí změnu velikosti virtuálního clusteru, všechny požadavky byly odeslány 5 + minut po dokončení změny velikosti virtuálního clusteru, protože tyto požadavky budou vyžadovat, aby se změna změnila před obnovením.

- **Operace vytvoření nebo škálování odeslané v okně s pěti minutami** se dávkují a spustí paralelně.<br/>**Příklad:** Pro všechny operace odeslané v intervalu 5 minut se provede jenom jedna změna velikosti virtuálního clusteru (měří se od okamžiku provedení první žádosti o operaci). Pokud se po odeslání prvního požadavku pošle více než 5 minut, bude se čekat na dokončení změny ve virtuálním clusteru, než se spustí spuštění.

> [!IMPORTANT]
> Operace správy, které jsou zablokovány kvůli jiné probíhající operaci, budou automaticky obnoveny, jakmile budou splněny podmínky pro pokračování. Není nutná žádná akce uživatele, aby bylo možné obnovit dočasně pozastavené operace správy.

## <a name="canceling-management-operations"></a>Rušení operací správy

Následující tabulka shrnuje možnosti zrušení konkrétních operací správy a typických celkových dob trvání:

Kategorie  |Operace  |Zrušitelný  |Odhadovaná doba trvání zrušení  |
|---------|---------|---------|---------|
|Nasazení |Vytvoření instance |No |  |
|Aktualizace |Horizontální navýšení kapacity úložiště instance (Pro obecné účely) |No |  |
|Aktualizace |Horizontální navýšení kapacity úložiště instance (Pro důležité obchodní informace) |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro obecné účely) |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro důležité obchodní informace) |Ano |90% dokončených operací za 5 minut. |
|Aktualizace |Instance instance služby instance (Pro obecné účely až Pro důležité obchodní informace a naopak) |Ano |90% dokončených operací za 5 minut. |
|Odstranit |Odstranění instance |No |  |
|Odstranit |Odstranění virtuálního clusteru (jako operace iniciované uživatelem) |No |  |

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Chcete-li zrušit operaci správy, přejděte do okna Přehled a klikněte na oznamovací políčko probíhající operace. Na pravé straně se zobrazí obrazovka s probíhající operací a bude k dispozici tlačítko pro zrušení operace. Po prvním kliknutí se zobrazí výzva, abyste znovu klikněte na tlačítko a potvrďte, že chcete operaci zrušit.

[![Zrušit operaci](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

Po odeslání a zpracování žádosti o zrušení se zobrazí oznámení, že odeslání zrušení bylo úspěšné.

Pokud je odeslání žádosti o zrušení úspěšné, operace správy se během několika minut zruší a výsledkem bude selhání.

![Výsledek operace zrušení](./media/management-operations-overview/canceling-operation-result.png)

Pokud žádost o zrušení selže nebo pokud není aktivní tlačítko Storno, znamená to, že operace správy přešla do stavu, který není možné zrušit a že se dokončí během několika minut. Operace správy bude pokračovat v provádění, dokud nebude dokončena.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud ještě nemáte nainstalované Azure PowerShell, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Chcete-li zrušit operaci správy, je nutné zadat název operace správy. Proto nejprve použijte příkaz Get pro načtení seznamu operací a pak zrušte konkrétní operaci.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Podrobné vysvětlení příkazů naleznete v tématu [Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) a [stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud ještě nemáte nainstalované rozhraní příkazového řádku Azure, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Chcete-li zrušit operaci správy, je nutné zadat název operace správy. Proto nejprve použijte příkaz Get pro načtení seznamu operací a pak zrušte konkrétní operaci.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Podrobné vysvětlení příkazů najdete v tématu [AZ SQL mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](instance-create-quickstart.md).
- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
