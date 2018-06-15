---
title: Nástroje pro Ansible pomocí Azure
description: Nainstalovat a používat jednotlivé nástroje pro Ansible s Azure
ms.service: ansible
keywords: ansible, azure, devops, nástroje, vs kód, kód v sadě visual studio, rozšíření
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28212047"
---
# <a name="tools-for-using-ansible-with-azure"></a>Nástroje pro Ansible pomocí Azure

Následující nástroje usnadnění práce s Ansible a Azure jednodušší a efektivnější.

## <a name="visual-studio-code-extension-for-ansible"></a>Rozšíření sady Visual Studio Code pro Ansible

[Visual Studio Code rozšíření pro Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) obsahuje několik funkcí použití Ansible z Visual Studio Code:

- Automatické doplňování Ansible direktivy, moduly a moduly plug-in z dokumentace Ansible podle typu.
- Kód zobrazení fragmenty při kliknutí na &lt;Ctrl >&lt;místo > při vytváření vašeho Ansible playbooks.
- Zvýraznění syntaxe zobrazí kódu playbook Ansible v různých barev a písem v souladu s YAML syntaxe.
- Ansible playbooks lze spustit z okna terminálu kódu Visual Studio.
    - (Jenom Windows) Ansible lze spustit z kontejner Docker.
    - (Linux a systému macOS) Ansible lze spustit z kontejner Docker nebo místní Ansible zařízení. 
- Ansible playbooks lze spustit z prostředí cloudu Azure.