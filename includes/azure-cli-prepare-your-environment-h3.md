---
ms.topic: include
ms.date: 09/10/2020
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.technology: azure-cli
ms.service: azure-cli
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01e1f956270ebbc9b9d5d0ebdfdff52875cafedf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000044"
---
### <a name="prepare-your-environment-for-the-azure-cli"></a>Příprava prostředí pro rozhraní příkazového řádku Azure

- Využijte [Azure Cloud Shell](../articles/cloud-shell/quickstart.md) s využitím prostředí Bash.

   [![Vložené spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)   
- Pokud tomu dáváte přednost, můžete [nainstalovat](/cli/azure/install-azure-cli) Azure CLI a spouštět referenční příkazy CLI.
   - Pokud používáte místní instalaci, přihlaste se s Azure CLI pomocí příkazu [az login](/cli/azure/reference-index#az-login).  Pokud chcete dokončit proces ověřování, postupujte podle kroků zobrazených na terminálu.  Další možnosti přihlášení jsou popsané v tématu [Přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli).
  - Po zobrazení výzvy nainstalujte rozšíření Azure CLI při prvním použití.  Další informace o rozšířeních najdete v tématu [Využití rozšíření v Azure CLI](/cli/azure/azure-cli-extensions-overview).
  - Spuštěním příkazu [az version](/cli/azure/reference-index?#az_version) zjistěte verzi a závislé knihovny, které jsou nainstalované. Pokud chcete upgradovat na nejnovější verzi, spusťte [az upgrade](/cli/azure/reference-index?#az_upgrade).