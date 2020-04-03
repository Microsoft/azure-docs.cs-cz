---
title: Poradce při potížích s baštou Azure | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak řešit potíže s Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 749d7125c013f419197ef8243d2475e612dc81b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619171"
---
# <a name="troubleshoot-azure-bastion"></a>Řešení potíží se službou Azure Bastion

Tento článek ukazuje, jak řešit potíže s Azure Bastion.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Nelze vytvořit skupinu zabezpečení sítě v síti AzureBastionSubnet.

**Otázka:** Při pokusu o vytvoření skupiny zabezpečení sítě v podsíti Azure Bastion se zobrazí následující chyba: *"Skupina <NSG name> zabezpečení sítě nemá potřebná pravidla pro Azure Bastion Subnet) .*

**A:** Pokud vytvoříte a použijete skupinu zabezpečení sítě pro *AzureBastionSubnet*, ujistěte se, že jste do souboru zabezpečení sítě přidali následující pravidla. Pokud tato pravidla nepřidáte, vytvoření/aktualizace sítě zabezpečení sítě se nezdaří.

1. Připojení k řídicí rovině – příchozí na 443 z GatewayManager
2. Protokolování diagnostiky a další – odchozí na 443 na AzureCloud (místní značky v rámci této značky služby ještě nejsou podporované.)
3. Cílový virtuální počítač – odchozí pro 3389 a 22 do virtuální sítě

Příklad pravidel nsg je k dispozici pro odkaz v [šabloně rychlého startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion).
Další informace najdete v [tématu NSG pokyny pro Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Nelze použít klíč SSH s Azure Bastion

**Otázka:** Při pokusu o procházení souboru klíče SSH se zobrazí následující chyba: *"SSH Private key must start with -----BEGIN RSA PRIVATE KEY----- and ends with -----END RSA PRIVATE KEY-----'*.

**A:** Azure Bastion podporuje pouze klíče RSA SSH v tomto okamžiku. Ujistěte se, že procházet soubor klíče, který je RSA soukromý klíč pro SSH, s zřízeným veřejným klíčem na cílovém virtuálním počítači. 

Jako příklad můžete použít následující příkaz k vytvoření nového klíče RSA SSH:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com"**

Výstup:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Nelze se přihlásit k virtuálnímu počítači dovirtuálního počítače se službou Windows pro připojení k doméně.

**Otázka:** Nemohu se připojit k virtuálnímu počítači se systémem Windows, který je připojen k doméně.

**A:** Azure Bastion podporuje přihlášení virtuálního počítače přilehlého k doméně pro přihlášení do domény založené na uživatelském jménu. Při zadávání přihlašovacích údajů domény na webu Azureusername@domainPortal použijte místo formátu doména\uživatelské jméno místo formátu *doména\uživatelské jméno.* To je podporováno pro virtuální počítače připojená k doméně nebo hybridní (připojení k doméně i připojení k Azure AD). Není podporována pro virtuální počítače pouze pro azure ad pouze.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problémy s přenosem souborů

**Otázka:** Je přenos souborů podporován pomocí Azure Bastion?

**A:** Přenos souborů není v současné době podporován. Pracujeme na přidání podpory.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Černá obrazovka na webu Azure Portal

**Otázka:** Když se pokusím připojit pomocí Azure Bastion, dostanu černou obrazovku na webu Azure Portal.

**A:** K tomu dochází, když je problém připojení k síti mezi webovým prohlížečem a Azure Bastion (váš klient internet ová lágy může blokovat provoz WebSockets nebo podobné) nebo mezi Bašta Azure a váš cílový virtuální počítač. Většina případů zahrnuje skupinu zabezpečení sítě použitou buď na AzureBastionSubnet, nebo na cílové podsíti virtuálního počítače, která blokuje provoz RDP/SSH ve vaší virtuální síti. Povolte provoz websockets na síti Internet firewall klienta a zkontrolujte skupiny zabezpečení v cílové podsíti virtuálních počítače.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v [nejčastějších dotazech](bastion-faq.md)k baště .