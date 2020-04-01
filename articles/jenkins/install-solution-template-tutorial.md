---
title: Kurz – vytvoření serveru Jenkins v Azure
description: V tomto kurzu nainstalujete Jenkinse na virtuální počítač Azure Linux ze šablony řešení Jenkins a vytvoříte ukázkovou aplikaci Java.
keywords: jenkins, azure, devops, portal, virtual machine, solution template
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274897"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Kurz: Vytvoření serveru Jenkins na virtuálním počítači Azure Linux 

Tento kurz ukazuje, jak nainstalovat [Jenkinse](https://jenkins.io) na virtuální počítač Ubuntu Linux s nástroji a moduly plug-in nakonfigurovanými pro práci s Azure. Až budete hotovi, budete mít v Azure spuštěný server Jenkins sestavující ukázkovou aplikaci v Javě z [GitHubu](https://github.com).

> [!div class="checklist"]
> * Instalace a konfigurace serveru Jenkins v Azure
> * Přístup ke konzoli Jenkins pomocí tunelu SSH
> * Vytvoření projektu freestyle
> * Kompilace kódu a balíček ukázkové aplikace

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]