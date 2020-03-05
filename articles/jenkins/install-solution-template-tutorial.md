---
title: Kurz – vytvoření serveru Jenkinse v Azure
description: V tomto kurzu nainstalujete Jenkinse na virtuální počítač Azure Linux ze šablony řešení Jenkinse a vytvoříte ukázkovou aplikaci Java.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274897"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Kurz: vytvoření serveru Jenkinse na virtuálním počítači Azure Linux 

V tomto kurzu se dozvíte, jak nainstalovat [Jenkinse](https://jenkins.io) na virtuálním počítači s Ubuntu Linux pomocí nástrojů a modulů plug-in nakonfigurovaných pro práci s Azure. Až budete hotovi, budete mít v Azure spuštěný server Jenkins sestavující ukázkovou aplikaci v Javě z [GitHubu](https://github.com).

> [!div class="checklist"]
> * Instalace a konfigurace serveru Jenkinse v Azure
> * Přístup ke konzole Jenkinse pomocí tunelu SSH
> * Vytvoření projektu Freestyle
> * Zkompilujte kód a zabalit ukázkovou aplikaci

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]