---
title: Řešení potíží s Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak řešit potíže s Azure bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 23b7a66afcc91cf1cf4a5dd9f720aad24ad40071
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133986"
---
# <a name="troubleshoot-azure-bastion"></a>Řešení potíží se službou Azure Bastion

V tomto článku se dozvíte, jak řešit potíže s Azure bastionu.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Nejde vytvořit NSG na AzureBastionSubnet.

**Otázka:** Při pokusu o vytvoření NSG v podsíti Azure bastionu se zobrazí následující chyba: *Skupina zabezpečení sítě nemá <NSG name> potřebná pravidla pro podsíť Azure bastionu AzureBastionSubnet*.

**A:** Pokud vytvoříte a použijete NSG na *AzureBastionSubnet*, ujistěte se, že jste přidali požadovaná pravidla do NSG. Seznam požadovaných pravidel najdete v tématu [práce s NSG přístupem a Azure bastionu](./bastion-nsg.md). Pokud tato pravidla nepřidáte, vytvoření nebo aktualizace NSG se nezdaří.

Příklad pravidel NSG je k dispozici pro referenci v šabloně pro [rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).
Další informace najdete v tématu [doprovodné materiály k NSG pro Azure bastionu](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Nejde použít klíč SSH s Azure bastionu.

**Otázka:** Při pokusu o procházení souboru klíče SSH se zobrazí následující chyba: *privátní klíč SSH musí začínat-----ZAčínat-----privátního klíče RSA a končit-----END-----privátního klíče RSA*.

**A:** Azure bastionu podporuje jenom klíče RSA SSH, v tomto okamžiku. Ujistěte se, že procházíte soubor klíče, který je privátním klíčem RSA pro SSH, s veřejným klíčem zřízeným na cílovém virtuálním počítači. 

Jako příklad můžete použít následující příkaz k vytvoření nového klíče RSA SSH:

**ssh-keygen-t RSA-b 4096-C " email@domain.com "**

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

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Nejde se přihlásit k virtuálnímu počítači připojenému k doméně Windows.

**Otázka:** Nedaří se mi připojit se k virtuálnímu počítači s Windows, který je připojený k doméně.

**A:** Azure bastionu podporuje přihlášení virtuálních počítačů připojených k doméně pro přihlašovací uživatelské jméno a heslo. Při zadávání přihlašovacích údajů domény v Azure Portal použijte username@domain k přihlášení formát UPN () místo formátu *doména \ uživatelské_jméno* . To je podporováno pro virtuální počítače připojené k doméně nebo Hybrid-JOINED (jak je připojeno k doméně i Azure AD). U virtuálních počítačů, které jsou jenom připojené k Azure AD, se nepodporuje.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Problémy s přenosem souborů

**Otázka:** Podporuje se přenos souborů s Azure bastionu?

**A:** Přenos souborů se v tuto chvíli nepodporuje. Pracujeme na přidání podpory.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Černá obrazovka v Azure Portal

**Otázka:** Při pokusu o připojení pomocí Azure bastionu nemůžu Connnect na cílový virtuální počítač a na Azure Portal se zobrazí černá obrazovka.

**A:** K tomu dojde v případě, že mezi webovým prohlížečem a službou Azure bastionu dojde k problémům se síťovým připojením (Internet Firewall klienta může blokovat provoz WebSockets nebo podobný) nebo mezi Azure bastionu a cílovým virtuálním počítačem. Většina případů zahrnuje NSG, která se používá buď na AzureBastionSubnet, nebo na cílové podsíti virtuálního počítače, která blokuje provoz RDP/SSH ve vaší virtuální síti. Povolte provoz WebSockets na klientské bráně Internet firewall a podívejte se na skupin zabezpečení sítě v cílové podsíti virtuálních počítačů.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [bastionu – Nejčastější dotazy](bastion-faq.md).
