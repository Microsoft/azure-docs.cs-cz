---
title: Horizontální navýšení a snížení kapacity Azure Database for PostgreSQL skupiny serverů se škálováním na více systému pomocí rozhraní CLI (azdata nebo kubectl)
description: Horizontální navýšení a snížení kapacity Azure Database for PostgreSQL skupiny serverů se škálováním na více systému pomocí rozhraní CLI (azdata nebo kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: b96f38d04fe3e3cb59fa75424ae588fe0e38f510
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935445"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Horizontální navýšení a snížení kapacity Azure Database for PostgreSQL skupiny serverů se škálováním na více systému pomocí rozhraní CLI (azdata nebo kubectl)



V některých případech může být nutné změnit vlastnosti nebo definici skupiny serverů. Příklad:

- Horizontální navýšení nebo snížení počtu virtuální jádraů, které používá každý koordinátor nebo pracovní uzly
- Horizontální navýšení nebo snížení kapacity paměti, kterou používá každý koordinátor nebo pracovní uzly

V této příručce se dozvíte, jak škálovat vCore a/nebo paměť.

Při vertikálním navýšení nebo zmenšení nastavení vCore nebo paměti skupiny serverů znamená, že máte možnost nastavit minimální nebo maximální hodnotu pro každé nastavení vCore a paměti. Pokud chcete konfigurovat skupinu serverů tak, aby používala určitý počet vCore nebo konkrétní velikost paměti, nastavíte minimální nastavení na hodnotu maximální hodnoty.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Zobrazit aktuální definici skupiny serverů

Pokud chcete zobrazit aktuální definici skupiny serverů a zjistit, jaká jsou aktuální nastavení vCore a paměti, spusťte některý z těchto příkazů:

### <a name="cli-with-azdata"></a>Rozhraní příkazového řádku s azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>Rozhraní příkazového řádku s kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Pokud jste vytvořili skupinu serverů PostgreSQL verze 11, spusťte ji `kubectl describe postgresql-11/<server group name>` .

Vrátí konfiguraci skupiny serverů. Pokud jste vytvořili skupinu serverů s výchozím nastavením, měla by se zobrazit následující definice:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpretace definice skupiny serverů

V definici skupiny serverů je oddíl, který přenáší nastavení min/max vCore na uzel a minimální/maximální paměť na uzel, oddíl **plánování** . V této části maximální nastavení bude trvalé v podčásti s názvem **omezení** a minimální nastavení budou trvalá v dílčí části s názvem **"požadavky"**.

Pokud nakonfigurujete minimální nastavení, která se liší od maximálního nastavení, konfigurace garantuje, že skupina serverů bude v případě potřeby přidělena požadovaným prostředkům. Nepřekračuje limity, které jste nastavili.

Prostředky (virtuální jádra a paměť), které budou ve skutečnosti používány skupinou serverů, jsou až do maximálního nastavení a závisí na zatíženích a prostředcích dostupných v clusteru. Pokud nechcete, aby se nastavení nastavila na maximální hodnotu, může skupina serverů využívat všechny prostředky, které cluster Kubernetes přiděluje k uzlům Kubernetes, na kterých je naplánovaná vaše skupina serverů.

Tato nastavení vCore a paměti se vztahují na každý uzel PostgreSQL v rámci škálování (uzel koordinátora a pracovní uzly). Ještě není podporované nastavování definic uzlů koordinátora a pracovních uzlů samostatně.

Ve výchozí konfiguraci je jenom minimální paměť nastavená na 256Mi, protože se jedná o minimální velikost paměti, která se doporučuje pro běh PostgreSQL s velkou škálou.

> [!NOTE]
> Minimální nastavení znamená, že skupina serverů bude toto minimum nutně používat. To znamená, že pokud je skupina serverů potřebuje, je zaručeno, že bude alespoň Toto minimum přiděleno. Předpokládejme například, že jsme nastavili `--minCpu 2` . Neznamená to, že skupina serverů bude používat vždy alespoň 2 virtuální jádra. Místo toho to znamená, že skupina závažnosti může začít používat méně než 2 virtuální jádra, pokud to nepotřebuje, aby bylo zaručeno, že se má přidělit aspoň 2 virtuální jádra, pokud ji potřebují později. To znamená, že cluster Kubernetes přiděluje prostředky jiným úlohám takovým způsobem, že může přidělit 2 virtuální jádra skupiny serverů, pokud ji někdy potřebují.

>[!NOTE]
>Před úpravou konfigurace systému se prosím ujistěte, že [tady](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities) seznámete s modelem prostředků Kubernetes.

## <a name="scale-up-the-server-group"></a>Horizontální navýšení kapacity skupiny serverů

Nastavení, které se chystáte nastavit, je nutné vzít v úvahu v konfiguraci, kterou jste nastavili pro cluster Kubernetes. Ujistěte se, že nenastavujete hodnoty, které váš cluster Kubernetes nebude moci vyhovět. To může vést k chybám nebo nepředvídatelnému chování. Příklad: Pokud stav skupiny serverů zůstane v _aktualizaci_ stavu po dlouhou dobu po změně konfigurace, může se jednat o indikaci, že nastavíte níže uvedené parametry na hodnoty, které cluster Kubernetes nemůže splnit. Pokud se jedná o tento případ, vraťte změnu nebo Přečtěte _troubleshooting_section.

Předpokládejme, že chcete škálovat definici skupiny serverů na:

- Min vCore = 2
- Maximální vCore = 4
- Minimální paměť = 512 MB
- Maximální velikost paměti = 1 GB

Použijte některý z následujících přístupů:

### <a name="cli-with-azdata"></a>Rozhraní příkazového řádku s azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

Příkaz se úspěšně spustí, když se zobrazí:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Podrobnosti o těchto parametrech získáte spuštěním `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>Rozhraní příkazového řádku s kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Tím přejdete do editoru VI, kde můžete procházet a měnit konfiguraci. K namapování požadovaného nastavení na název pole ve specifikaci použijte následující:

- Min vCore = 2-> scheduling\default\resources\requests\cpu
- Max vCore = 4 – > scheduling\default\resources\limits\cpu
- Minimální paměť = 512 MB – > scheduling\default\resources\requests\cpu
- Maximální velikost paměti = 1 GB-> scheduling\default\resources\limits\cpu

Pokud nejste obeznámeni s editorem VI, přečtěte si popis příkazů, které [možná budete potřebovat:](https://www.computerhope.com/unix/uvi.htm)
- režim úprav: `i`
- pohyb se šipkami
- _stop úpravy: `esc`
- _exit bez uložení: `:qa!`
- _exit po uložení: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>Zobrazit definici horizontálního navýšení kapacity skupiny serverů

Spusťte příkaz znovu, aby se zobrazila definice skupiny serverů, a ověřte, že je nastavené tak, jak budete chtít:

### <a name="cli-with-azdata"></a>Rozhraní příkazového řádku s azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>Rozhraní příkazového řádku s kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Pokud jste vytvořili skupinu serverů PostgreSQL verze 11, spusťte ji `kubectl describe postgresql-11/<server group name>` .


Zobrazí se nová definice skupiny serverů:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>Horizontální navýšení kapacity skupiny serverů

Pro horizontální navýšení kapacity skupiny serverů spusťte stejný příkaz, ale nastavte menší hodnoty pro nastavení, které chcete škálovat. Chcete-li odebrat požadavky a omezení, zadejte její hodnotu jako prázdný řetězec.

## <a name="next-steps"></a>Další kroky

- [Horizontální navýšení kapacity Azure Database for PostgreSQL skupiny serverů s škálovatelným škálováním](scale-out-postgresql-hyperscale-server-group.md)
- [Koncepty konfigurace úložiště a Kubernetes úložiště](storage-configuration.md)
- [Rozšiřování deklarací trvalých svazků](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Model prostředků Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
