---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482116"
---
1. Ujistěte se, že máte [účet Azure s aktivním předplatným](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Nainstalujte [Python 2.7 + nebo 3.5.3 +](https://www.python.org/downloads).

1. Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

1. Postupujte podle pokynů v tématu [Konfigurace ověřování pro místní vývoj](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), pomocí kterého vytvoříte místní instanční objekt a zpřístupníte ho Azure Key Vault klientovi pro Python prostřednictvím proměnných prostředí. 

    Při spouštění kódu přímo v Azure se nepotřebuje samostatný instanční objekt, pokud aplikace používá spravovanou identitu.

1. V terminálu nebo na příkazovém řádku vytvořte vhodnou složku projektu a pak vytvořte a aktivujte virtuální prostředí Pythonu, jak je popsáno v tématu [použití virtuálních prostředí Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) .

1. Nainstalujte Azure Active Directory knihovnu identit:

    ```terminal
    pip install azure.identity
    ```
