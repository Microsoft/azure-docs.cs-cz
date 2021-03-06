---
title: Kvóty a omezení služeb
description: Přečtěte si o výchozích Azure Batch kvótách, omezeních a omezeních a o zvýšení kvóty.
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553545"
---
# <a name="batch-service-quotas-and-limits"></a>Kvóty a omezení služby Batch

Stejně jako u jiných služeb Azure platí omezení pro určité prostředky přidružené ke službě Batch. Mnohé z těchto omezení jsou výchozími kvótami, které Azure používá na úrovni předplatného nebo účtu.

Při návrhu a navýšení kapacity úloh služby Batch mějte na paměti tyto kvóty. Například pokud váš fond nedosahuje cílového počtu výpočetních uzlů, které jste zadali, možná jste dosáhli limitu základní kvóty pro účet Batch.

Můžete spouštět více úloh služby Batch v jednom účtu Batch nebo distribuovat úlohy mezi účty Batch, které jsou ve stejném předplatném, ale v různých oblastech Azure.

Pokud máte v plánu provozovat produkční úlohy ve službě Batch, možná budete muset zvýšit jednu nebo více kvót nad výchozí hodnotou. Pokud chcete zvýšit kvótu, můžete [požádat o zvýšení kvóty](#increase-a-quota) zdarma.

## <a name="resource-quotas"></a>Kvóty prostředků

Kvóta je limit, nikoli záruka na kapacitu. Pokud máte velké nároky na kapacitu, obraťte se prosím na podporu Azure.

Všimněte si také, že kvóty nejsou Garantované hodnoty. Kvóty se můžou lišit v závislosti na změnách ze služby Batch nebo na žádost uživatele o změnu hodnoty kvóty.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Základní kvóty

### <a name="cores-quotas-in-batch-service-mode"></a>Kvóty jader v režimu služby Batch

Pro každou řadu virtuálních počítačů podporovanou službou Batch existují základní kvóty. Tyto klíčové kvóty se zobrazí na stránce **kvóty** v Azure Portal. Omezení kvót řady virtuálních počítačů je možné aktualizovat pomocí žádosti o podporu, jak je popsáno níže. U vyhrazených uzlů Batch vynutila omezení základní kvóty pro každou řadu virtuálních počítačů a také celkovou kvótu jádra pro celý účet Batch. V případě uzlů s nízkou prioritou vynutila dávka jenom celkovou základní kvótu pro účet Batch bez rozdílů mezi různými řadami virtuálních počítačů.

### <a name="cores-quotas-in-user-subscription-mode"></a>Kvóty jader v režimu předplatného uživatele

Pokud jste vytvořili [účet Batch](accounts.md) s režimem přidělování fondů nastaveným na **předplatné uživatele**, virtuální počítače a další prostředky služby Batch se vytvoří přímo ve vašem předplatném při vytvoření nebo změně velikosti fondu. Kvóty Azure Batch Core se nevztahují a používají se a vynutily kvóty v předplatném pro regionální výpočetní jádra, výpočetní jádra pro jednotlivé řady a další prostředky.

Další informace o těchto kvótách najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Omezení velikosti fondu

Omezení velikosti fondu nastavuje služba Batch. Na rozdíl od [kvót prostředků](#resource-quotas)se tyto hodnoty nedají změnit. Omezení odlišná od standardní kvóty mají jenom fondy, které mají komunikaci mezi uzly a vlastní image.

| **Prostředek** | **Maximální limit** |
| --- | --- |
| **Výpočetní uzly ve [fondu s povolenými komunikacemi mezi uzly](batch-mpi.md)**  ||
| Režim přidělování fondů služby Batch | 100 |
| Režim přidělování fondů předplatných dávky | 80 |
| **Výpočetní uzly ve [fondu vytvořené pomocí prostředku spravované image](batch-custom-images.md)**<sup>1</sup> ||
| Vyhrazené uzly | 2000 |
| Uzly s nízkou prioritou | 1000 |

<sup>1</sup> pro fondy, které nejsou povolené komunikace mezi uzly.

## <a name="other-limits"></a>Další omezení

Tato dodatečná omezení nastavuje služba Batch. Na rozdíl od [kvót prostředků](#resource-quotas)nelze tyto hodnoty změnit.

| **Prostředek** | **Maximální limit** |
| --- | --- |
| [Souběžné úkoly](batch-parallel-node-tasks.md) na výpočetní uzel | 4 x počet jader uzlů |
| Počet [aplikací](batch-application-packages.md) na účet Batch | 20 |
| Balíčky aplikací na aplikaci | 40 |
| Balíčky aplikací na fond | 10 |
| Maximální doba života úlohy | 180 dnů<sup>1</sup> |
| Počet [připojení](virtual-file-mount.md) na výpočetní uzel | 10 |
| Certifikáty na fond | 12 |

<sup>1</sup> maximální doba života úkolu, od kdy se přidá do úlohy po jeho dokončení, je 180 dní. Dokončené úlohy trvají po dobu sedmi dnů; data pro úlohy, které nejsou dokončená v rámci maximální doby života, nejsou dostupná.

## <a name="view-batch-quotas"></a>Zobrazit kvóty dávky

Zobrazení kvót účtu Batch v [Azure Portal](https://portal.azure.com):

1. Vyberte **účty Batch** a pak vyberte účet Batch, na který vás zajímáte.
1. V nabídce účtu Batch vyberte **kvóty** .
1. Zobrazit kvóty, které jsou aktuálně aplikovány na účet Batch.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Snímek obrazovky zobrazující kvóty účtu Batch v Azure Portal.":::

## <a name="increase-a-quota"></a>Zvýšení kvóty

Můžete požádat o zvýšení kvóty pro účet Batch nebo předplatné pomocí [Azure Portal](https://portal.azure.com) nebo pomocí [REST API kvót Azure](#azure-quota-rest-api).

Typ zvýšení kvóty závisí na režimu přidělování fondů účtu Batch. Chcete-li požádat o zvýšení kvóty, je nutné zahrnout řadu virtuálních počítačů, pro kterou chcete kvótu zvýšit. Při použití zvýšení kvóty se tato kvóta použije na všechny řady virtuálních počítačů.

Po odeslání žádosti o podporu vás bude kontaktovat podpora Azure. Žádosti o kvótu se můžou dokončit během několika minut nebo do dvou pracovních dnů.

### <a name="azure-portal"></a>portál Azure

1. Na stránce **kvóty** vyberte **zvýšení kvóty žádostí**. Alternativně můžete na řídicím panelu portálu vybrat dlaždici **Nápověda a podpora** (nebo z otazníku (**?**) v pravém horním rohu portálu) a pak vybrat **nový požadavek na podporu.**

1. **Základní informace**:

    1. Jako **typ problému** vyberte **omezení služby a předplatné (kvóty)**.
    1. Vyberte své předplatné.
    1. Jako **typ kvóty** vyberte **Batch**.
    1. Pokračujte výběrem tlačítka **Další**.

1. Oblast **Details** (Podrobnosti) obsahuje:

    1. V části **zadat podrobnosti** určete umístění, typ kvóty a účet Batch (Pokud je k dispozici) a pak vyberte kvótu, která se má zvýšit.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Snímek obrazovky s podrobnostmi o kvótě při žádosti o zvýšení kvóty":::

       Mezi typy kvót patří:

       - **Na účet Batch**  
         Tuto možnost použijte, pokud chcete požádat o kvótu, která se zvyšuje pro konkrétní účet Batch, včetně základních a s nízkou prioritou, a počtu úloh a fondů.

         Pokud vyberete tuto možnost, zadejte účet Batch, na který se má tato žádost vztahovat, a pak vyberte kvótu, kterou chcete aktualizovat. Zadejte nový limit, který požadujete pro každý prostředek.

         Kvóta s nízkou prioritou je jednou hodnotou v rámci všech řad virtuálních počítačů. Pokud potřebujete omezené skladové jednotky, musíte vybrat **jádra s nízkou prioritou** a zahrnout do žádosti rodiny virtuálních počítačů.

       - **Všechny účty v této oblasti**  
         Tuto možnost použijte, pokud chcete požádat o zvýšení kvóty, které platí pro všechny účty Batch v oblasti, například počet účtů Batch na oblast a předplatné.

    1. V rámci **metody podpory** vyberte **závažnost** podle svého [obchodního dopadu](https://aka.ms/supportseverity) a preferovaného způsobu kontaktu a jazyka podpory.

    1. V **kontaktní údaje** ověřte a zadejte požadované kontaktní údaje.

1. Vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit** a odešlete žádost o podporu.

### <a name="azure-quota-rest-api"></a>REST API kvóty Azure

Pomocí REST API kvóty Azure můžete požádat o zvýšení kvóty na úrovni předplatného nebo na úrovni účtu Batch.

Podrobnosti a příklady najdete v tématu [vyžádání zvýšení kvóty pomocí REST API podpory Azure](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>Související kvóty pro fondy virtuálních počítačů

[Fondy služby Batch v konfiguraci virtuálního počítače nasazené ve virtuální síti Azure](batch-virtual-network.md) automaticky přidělují další síťové prostředky Azure. Tyto prostředky se vytvoří v předplatném, které obsahuje virtuální síť, která se zadala při vytváření fondu Batch.

Pro každý uzel fondu 100 ve virtuální síti se vytvoří tyto prostředky:

- Jedna [Skupina zabezpečení sítě](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Jedna [Veřejná IP adresa](../virtual-network/public-ip-addresses.md)
- Jeden [Nástroj pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md)

Pro tyto prostředky platí omezení [kvót prostředků](../azure-resource-manager/management/azure-subscription-service-limits.md) předplatného. Pokud plánujete nasazení rozsáhlých fondů ve virtuální síti, může být nutné požádat o zvýšení kvóty u jednoho nebo více těchto prostředků.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [pracovních postupech služby Batch a primárních prostředcích](batch-service-workflow-features.md) , jako jsou fondy, uzly, úlohy a úkoly.
- Přečtěte si o [omezeních, kvótách a omezeních předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).