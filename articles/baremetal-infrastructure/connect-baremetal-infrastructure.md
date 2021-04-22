---
title: Propojení instancí infrastruktury BareMetal v Azure
description: Přečtěte si, jak identifikovat a pracovat s BareMetal instancemi v Azure Portal nebo v rozhraní příkazového řádku Azure.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871646"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Propojení instancí infrastruktury BareMetal v Azure

Tento článek ukazuje, jak [Azure Portal](https://portal.azure.com/) zobrazuje [instance BareMetal](concepts-baremetal-infrastructure-overview.md). Tento článek také ukazuje, co můžete dělat v Azure Portal s nasazenými instancemi infrastruktury BareMetal. 
 
## <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků
Poskytovatel prostředků Azure pro instance BareMetal poskytuje viditelnost instancí v Azure Portal. Ve výchozím nastavení předplatné Azure, které používáte pro nasazení instancí BareMetal, zaregistruje poskytovatele prostředků *BareMetalInfrastructure* . Pokud nevidíte nasazené instance BareMetal, musíte poskytovatele prostředků zaregistrovat u svého předplatného. 

Poskytovatele prostředků instance BareMetal můžete zaregistrovat pomocí Azure Portal nebo Azure CLI.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)
 
V Azure Portal budete muset uvést své předplatné a pak dvakrát kliknout na předplatné, které jste použili k nasazení instancí BareMetal.
 
1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. V nabídce webu Azure Portal vyberte **Všechny služby**.

1. V poli **všechny služby** zadejte **předplatné** a pak vyberte **předplatná**.

1. Vyberte předplatné ze seznamu předplatných.

1. Vyberte **poskytovatelé prostředků** a do hledání zadejte **BareMetalInfrastructure** . Poskytovatel prostředků by měl být **zaregistrován**, jak ukazuje obrázek.
 
>[!NOTE]
>Pokud poskytovatel prostředků není zaregistrovaný, vyberte **zaregistrovat**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Snímek obrazovky znázorňující zaregistrované instance BareMetal":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li začít používat rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Přihlaste se k předplatnému Azure, které používáte pro nasazení instance BareMetal prostřednictvím rozhraní příkazového řádku Azure CLI. Zaregistrujte `BareMetalInfrastructure` poskytovatele prostředků pomocí příkazu [AZ Provider Register](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

K zobrazení všech dostupných zprostředkovatelů můžete použít příkaz [AZ Provider list](/cli/azure/provider#az_provider_list) .

---

Další informace o poskytovatelích prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal instancí v Azure Portal
 
Když odešlete žádost o nasazení instance BareMetal, zadáte předplatné Azure, které se připojujete k instancím BareMetal. Použijte stejné předplatné, které používáte k nasazení aplikační vrstvy, která funguje s instancemi BareMetal.
 
Během nasazování instancí BareMetal se v předplatném Azure, které jste použili v žádosti o nasazení, vytvoří nová [Skupina prostředků Azure](../azure-resource-manager/management/manage-resources-portal.md) . Tato nová skupina prostředků obsahuje seznam všech instancí BareMetal, které jste v tomto předplatném nasadili.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V předplatném BareMetal ve Azure Portal vyberte **skupiny prostředků**.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Snímek obrazovky zobrazující seznam skupin prostředků":::

1. V seznamu vyhledejte novou skupinu prostředků.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Snímek obrazovky zobrazující instanci BareMetal v seznamu filtrovaných skupin prostředků" lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Můžete filtrovat podle předplatného, které jste použili k nasazení instance BareMetal. Po filtrování na správné předplatné můžete mít dlouhý seznam skupin prostředků. Vyhledejte ho s následnou opravou **-TXXX** , kde xxx je tři číslice jako **-T250**.

1. Vyberte novou skupinu prostředků, abyste zobrazili její podrobnosti. Obrázek ukazuje jednu instanci BareMetal nasazenou.
   
   >[!NOTE]
   >Pokud jste v rámci stejného předplatného Azure nasadili několik tenantů instance BareMetal, zobrazí se několik skupin prostředků Azure.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit všechny instance BareMetal, spusťte příkaz [AZ baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) pro vaši skupinu prostředků:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> `--output`Parametr je globální parametr, který je k dispozici pro všechny příkazy. Hodnota **tabulky** prezentuje výstup v popisném formátu. Další informace najdete v tématu [formáty výstupu pro příkazy rozhraní příkazového řádku Azure CLI](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Zobrazení atributů jedné instance

Můžete zobrazit podrobnosti jedné instance.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V seznamu instancí BareMetal vyberte jednu instanci, kterou chcete zobrazit.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Snímek obrazovky znázorňující atributy instance BareMetal jedné instance" lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Atributy v imagi se neliší od atributů virtuálních počítačů Azure. Vlevo uvidíte skupinu prostředků, oblast Azure a název a ID předplatného. Pokud jste přiřadili značky, uvidíte je také tady. Ve výchozím nastavení nejsou k instancím BareMetal přiřazeny štítky.
 
Napravo se zobrazí název instance BareMetal, operační systém (OS), IP adresa a SKU, které zobrazují počet vláken procesoru a paměti. Zobrazí se také stav napájení a verze hardwaru (revize razítka instance BareMetal). Stav napájení označuje, zda je hardwarová jednotka zapnutá nebo vypnutá. Podrobnosti o operačním systému ale neoznačují, jestli jsou spuštěné a spuštěné.
 
Možné hardwarové Revize:

* Revize 3 (rev 3)

* Revize 4 (Rev 4)
 
* Revize 4,2 (Rev 4,2)
 
>[!NOTE]
>Rev 4,2 je nejnovější přepálená infrastruktura BareMetal s využitím existující architektury Rev 4. Rev 4 poskytuje užší blízkost pro hostitele virtuálních počítačů Azure. Má významná vylepšení latence sítě mezi virtuálními počítači Azure a instancemi SAP HANA. Pomocí Azure Portal můžete přistupovat k instancím služby BareMetal a spravovat je. Další informace najdete v tématu [infrastruktura BareMetal v Azure](concepts-baremetal-infrastructure-overview.md).

 
Na pravé straně se také nachází název [skupiny umístění s blízkostií Azure](../virtual-machines/co-location.md) , který se vytvoří automaticky pro každou nasazenou instanci BareMetal. Pokud nasadíte virtuální počítače Azure, které hostují aplikační vrstvu, odkažte na skupinu umístění blízkosti. Když použijete skupinu umístění blízkosti přidruženou k instanci BareMetal, zajistíte, že se virtuální počítače Azure nasadí blízko do instance BareMetal.
 
>[!TIP]
>Pokud chcete vrstvu aplikace najít ve stejném datovém centru Azure jako revizi 4. x, přečtěte si téma [skupiny umístění blízkosti Azure pro optimální latenci sítě](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit podrobnosti o instanci BareMetal, spusťte příkaz [AZ baremetalinstance show](/cli/azure/baremetalinstance#az_baremetalinstance_show) :

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Pokud si nejste jisti názvem instance, spusťte `az baremetalinstance list` příkaz, který je popsaný výše.

---
 
## <a name="check-activities-of-a-single-instance"></a>Kontrolovat aktivity jedné instance
 
Můžete kontrolovat aktivity jedné instance BareMetal. Jedna z hlavních aktivit, které jsou zaznamenány, se restartuje instance. Uvedená data zahrnují stav aktivity, časové razítko, aktivované aktivity, ID předplatného a uživatele Azure, který aktivitu aktivoval.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Snímek obrazovky znázorňující aktivity instance BareMetal" lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Změny v metadatech instance v Azure se také zaznamenávají v protokolu aktivit. Kromě iniciace restartování můžete zobrazit aktivitu **BareMetallnstances zápisu**. Tato aktivita neprovede žádné změny v samotné instanci BareMetal, ale dokumentuje změny v metadatech jednotky v Azure.
 
Další zaznamenaná aktivita je při přidání nebo odstranění [značky](../azure-resource-manager/management/tag-resources.md) instance.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Přidání a odstranění značky Azure do instance

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)
 
Můžete přidat značky Azure do instance BareMetal nebo je odstranit. Značky se přiřazují stejně jako při přiřazování značek k virtuálním počítačům. Stejně jako u virtuálních počítačů existují značky v metadatech Azure. Značky mají stejná omezení pro instance BareMetal jako pro virtuální počítače.
 
Odstraňování značek funguje také stejně jako u virtuálních počítačů. Použití a odstranění značky je uvedeno v protokolu aktivit instance BareMetal.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Přiřazení značek k instancím BareMetal funguje stejně jako přiřazení značek pro virtuální počítače. Stejně jako u virtuálních počítačů existují značky v metadatech Azure. Značky mají stejná omezení pro instance BareMetal jako pro virtuální počítače.

Chcete-li přidat značky do instance BareMetal, spusťte příkaz [AZ baremetalinstance Update](/cli/azure/baremetalinstance#az_baremetalinstance_update) :

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Pro odebrání značky použijte stejný příkaz:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Zkontroluje vlastnosti instance.
 
Když získáte instance, můžete přejít do části Properties (vlastnosti) a zobrazit shromážděná data o instancích. Shromažďovaná data zahrnují připojení Azure, back-end úložiště, ID okruhu ExpressRoute, jedinečné ID prostředku a ID předplatného. Tyto informace použijete v žádostech o podporu nebo při nastavování konfigurace snímku úložiště.
 
Další důležité informace najdete v části IP adresa úložiště NFS. Izoluje vaše úložiště do svého **tenanta** v zásobníku instance BareMetal. Tuto IP adresu použijete při úpravách [konfiguračního souboru pro zálohy snímků úložiště](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Snímek obrazovky zobrazující nastavení vlastnosti instance BareMetal" lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Restartování instance BareMetal pomocí Azure Portal

Existují různé situace, kdy operační systém nedokončí restart, což vyžaduje restartování instance BareMetal.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Můžete provést restartování instance přímo z Azure Portal:
 
Vyberte **restartovat** a potvrďte restartování kliknutím na **Ano** .
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Snímek obrazovky ukazující, jak restartovat instanci BareMetal":::
 
Po restartování instance BareMetal se dostanou zpoždění. Během této prodlevy se stav napájení přesune od **začátku** do **začátku**, což znamená, že se operační systém kompletně spustil. V důsledku toho se po restartu můžete do jednotky přihlašovat až potom, co se stav přepne na **zahájeno**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete restartovat instanci BareMetal, použijte příkaz [AZ baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>V závislosti na velikosti paměti v instanci BareMetal může restartování a restartování hardwaru a operačního systému trvat až jednu hodinu.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Otevření žádosti o podporu pro instance BareMetal
 
Žádosti o podporu můžete odeslat konkrétně pro instance BareMetal.
1. V Azure Portal v části **pomoc a podpora** vytvořte **[novou žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support)** a zadejte pro lístek následující informace:
 
   - **Typ problému:** Vyberte typ problému.
 
   - **Předplatné:** Vyberte své předplatné.
 
   - **Služba:** Infrastruktura BareMetal
 
   - **Prostředek:** Zadejte název instance.
 
   - **Shrnutí:** Zadejte souhrn vaší žádosti.
 
   - **Typ problému:** Vyberte typ problému.
 
   - **Podtyp problému:** Vyberte podtyp pro daný problém.

1. Vyberte kartu **řešení** a najděte řešení problému. Pokud nemůžete najít řešení, pokračujte na další krok.

1. Vyberte kartu **Podrobnosti** a vyberte, jestli se jedná o problémy s virtuálními počítači nebo BareMetal instancemi. Tyto informace pomáhají nasměrovat požadavek na podporu na správné odborníky.

1. Určete, kdy problém začal a vyberte oblast instance.

1. Zadejte další podrobnosti o žádosti a v případě potřeby nahrajte soubor.

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.
 
Zástupce podpory může potvrdit vaši žádost až pět pracovních dnů.

## <a name="next-steps"></a>Další kroky

Další informace o úlohách:

- [Co je SAP HANA v Azure (velké instance)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
