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
ms.openlocfilehash: 6f42f712ecca77c00020304b63f5a1b0dbd77ad0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172316"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Instalace klientských nástrojů pro nasazování a správu datových služeb s podporou služby Azure Arc

> [!IMPORTANT]
> Pokud aktualizujete na novou měsíční verzi, nezapomeňte aktualizovat taky na nejnovější verzi Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Nástroj a [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] rozšíření Azure Arc pro Azure Data Studio.

Tento dokument vás provede kroky pro instalaci [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , Azure Data Studio, rozhraní příkazového řádku Azure CLI (AZ) a nástroj KUBERNETES CLI (kubectl) na klientském počítači.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Nástroje pro vytváření a správu datových služeb s povoleným obloukem Azure 

Následující tabulka obsahuje seznam běžných nástrojů vyžadovaných k vytváření a správě datových služeb s povoleným obloukem Azure a instalaci těchto nástrojů:

| Nástroj | Povinné | Popis | Instalace |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | Ano | Nástroj příkazového řádku pro instalaci a správu clusteru velkých objemů dat. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] zahrnuje také nástroj příkazového řádku pro připojení a dotazování instancí Azure SQL a SQL Server a Postgres serverů pomocí příkazů `azdata sql query` (spustit jeden dotaz z příkazového řádku), `azdata sql shell` (interaktivní prostředí) `azdata postgres query` a `azdata postgres shell` . | [Instalace](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Ano | Nástroj pro bohatou práci pro připojení a dotazování nejrůznějších databází, včetně Azure SQL, SQL Server, PostrgreSQL a MySQL. Rozšíření pro Azure Data Studio poskytují prostředí pro správu datových služeb s povoleným ARC Azure. | [Instalace](/sql/azure-data-studio/download-azure-data-studio) |
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] rozšíření pro Azure Data Studio | Ano | Rozšíření pro Azure Data Studio, které se nainstaluje, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Pokud ho ještě nemáte.| Instalace z galerie rozšíření v Azure Data Studio.|
| Rozšíření Azure ARC pro Azure Data Studio | Ano | Rozšíření pro Azure Data Studio, které poskytuje prostředí pro správu datových služeb s podporou ARC Azure. Existuje závislost na [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] rozšíření pro Azure Data Studio. | Instalace z galerie rozšíření v Azure Data Studio.|
| Rozšíření PostgreSQL v Azure Data Studio | Ne | Rozšíření PostgreSQL pro Azure Data Studio, které poskytuje možnosti správy pro PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Instalace z galerie rozšíření v Azure Data Studio.|
| Azure CLI (AZ)<sup>1</sup> | Ano | Moderní rozhraní příkazového řádku pro správu služeb Azure. Používá se s AKS nasazeními a k nahrání inventáře datových služeb s povoleným ARC Azure a fakturační data do Azure. ([Další informace](/cli/azure/)). | [Instalace](/cli/azure/install-azure-cli) |
| Kubernetes CLI (kubectl)<sup>2</sup> | Ano | Nástroj příkazového řádku pro správu clusteru Kubernetes ([Další informace](https://kubernetes.io/docs/tasks/tools/install-kubectl/)) | [Systém Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| kudrlinkou <sup>3</sup> | Vyžaduje se pro některé ukázkové skripty. | Nástroj příkazového řádku pro přenos dat s adresami URL. | [Systém Windows](https://curl.haxx.se/windows/) \| Linux: instalace balíčku s kudrlinkou |
| součet | Vyžaduje se pro nasazení Red Hat OpenShift a Azure RedHat OpenShift. |`oc` je otevřené rozhraní příkazového řádku (CLI) příkazu Shift. | [Instalace rozhraní příkazového řádku](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> musíte používat Azure CLI verze 2.0.4 nebo novější. `az --version`V případě potřeby vyhledejte verzi.

<sup>2</sup> je nutné použít `kubectl` verzi 1,13 nebo novější. Také verze nástroje `kubectl` by měla být plus nebo minus jedna dílčí verze clusteru Kubernetes. Pokud chcete nainstalovat určitou verzi na `kubectl` klienta, přečtěte si téma [instalace `kubectl` binárního souboru prostřednictvím formátu kudrlinkou](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (ve windows 10, použití cmd.exe, ne Windows PowerShellu pro spuštění oblé).

<sup>3</sup> Pokud používáte PowerShell, je k rutině Invoke-WebRequest přiřazen alias.

## <a name="next-steps"></a>Další kroky

[Vytvoření kontroleru dat ARC Azure](create-data-controller.md)