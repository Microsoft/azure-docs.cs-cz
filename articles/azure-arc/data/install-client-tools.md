---
title: Instalace klientských nástrojů
description: Nainstalujte azdata, kubectl, Azure CLI, psql, Azure Data Studio (Insider) a rozšíření ARC pro Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d61bc99e851b28712262dba8512c06b6e8872c0e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108214"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Instalace klientských nástrojů pro nasazování a správu datových služeb s podporou služby Azure Arc

> [!IMPORTANT]
> Pokud aktualizujete na novou měsíční verzi, nezapomeňte taky aktualizovat na nejnovější verzi Azure Data Studio, nástroj Azure Data CLI (azdata) a Azure Data CLI a rozšíření Azure ARC pro Azure Data Studio.

Tento dokument vás provede kroky pro instalaci rozhraní příkazového řádku Azure (azdata), Azure Data Studio, rozhraní příkazového řádku Azure CLI (AZ) a Kubernetes CLI Tool (kubectl) na klientském počítači.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Nástroje pro vytváření a správu datových služeb s povoleným obloukem Azure 

Následující tabulka obsahuje seznam běžných nástrojů vyžadovaných k vytváření a správě datových služeb s povoleným obloukem Azure a instalaci těchto nástrojů:

| Nástroj | Povinné | Popis | Instalace |
|---|---|---|---|
| Azure Data CLI (azdata) | Ano | Nástroj příkazového řádku pro instalaci a správu clusteru velkých objemů dat. Azure Data CLI také obsahuje nástroj příkazového řádku pro připojení k Azure SQL a SQL Server instancí a Postgres serverů a jejich dotazování pomocí příkazů `azdata sql query` (spuštění jednoho dotazu z příkazového řádku), `azdata sql shell` (interaktivní prostředí) `azdata postgres query` a `azdata postgres shell` . | [Instalace](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Ano | Nástroj pro bohatou práci pro připojení a dotazování nejrůznějších databází, včetně Azure SQL, SQL Server, PostrgreSQL a MySQL. Rozšíření pro Azure Data Studio poskytují prostředí pro správu datových služeb s povoleným ARC Azure. | [Instalace](/sql/azure-data-studio/download-azure-data-studio) |
| Rozšíření Azure Data CLI pro Azure Data Studio | Ano | Rozšíření pro Azure Data Studio, které nainstaluje Azure Data CLI, pokud ho ještě nemáte.| Instalace z galerie rozšíření v Azure Data Studio.|
| Rozšíření Azure ARC pro Azure Data Studio | Ano | Rozšíření pro Azure Data Studio, které poskytuje prostředí pro správu datových služeb s podporou ARC Azure. Existuje závislost na rozšíření Azure Data CLI pro Azure Data Studio. | Instalace z galerie rozšíření v Azure Data Studio.|
| Rozšíření PostgreSQL v Azure Data Studio | Ne | Rozšíření PostgreSQL pro Azure Data Studio, které poskytuje možnosti správy pro PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Instalace z galerie rozšíření v Azure Data Studio.|
| Azure CLI (AZ)<sup>1</sup> | Ano | Moderní rozhraní příkazového řádku pro správu služeb Azure. Používá se s AKS nasazeními a k nahrání inventáře datových služeb s povoleným ARC Azure a fakturační data do Azure. ([Další informace](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Instalace](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Ano | Nástroj příkazového řádku pro správu clusteru Kubernetes ([Další informace](https://kubernetes.io/docs/tasks/tools/install-kubectl/)) | [Systém Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| kudrlinkou <sup>3</sup> | Vyžaduje se pro některé ukázkové skripty. | Nástroj příkazového řádku pro přenos dat s adresami URL. | [Systém Windows](https://curl.haxx.se/windows/) \| Linux: instalace balíčku s kudrlinkou |
| součet | Vyžaduje se pro nasazení Red Hat OpenShift a Azure RedHat OpenShift. |`oc` je otevřené rozhraní příkazového řádku (CLI) příkazu Shift. | [Instalace rozhraní příkazového řádku](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> musíte používat Azure CLI verze 2.0.4 nebo novější. `az --version`V případě potřeby vyhledejte verzi.

<sup>2</sup> je nutné použít `kubectl` verzi 1,13 nebo novější. Také verze nástroje `kubectl` by měla být plus nebo minus jedna dílčí verze clusteru Kubernetes. Pokud chcete nainstalovat určitou verzi na `kubectl` klienta, přečtěte si téma [instalace `kubectl` binárního souboru prostřednictvím formátu kudrlinkou](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (ve windows 10, použití cmd.exe, ne Windows PowerShellu pro spuštění oblé).

<sup>3</sup> Pokud používáte PowerShell, je k rutině Invoke-WebRequest přiřazen alias.

## <a name="next-steps"></a>Další kroky

[Vytvoření kontroleru dat ARC Azure](create-data-controller.md)