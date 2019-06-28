---
title: Inicializace hardwaru – Microsoft Azure FXT hrany vyfiltrovat
description: Nastavení počátečního hesla na uzlech Azure FXT hrany vyfiltrovat
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450293"
---
# <a name="tutorial-set-hardware-passwords"></a>Kurz: Sada hardwarových hesla

Prvním je až do uzlu Azure FXT hrany Filer výkonu, musíte nastavit kořenové heslo. Hardwarové uzly nejsou součástí výchozí heslo. 

Síťové porty jsou zakázány až poté, co je nastavit heslo a přihlášení uživatele root.

Proveďte tento krok po instalaci a kabeláž uzlu, ale před pokusem o vytvoření clusteru. 

Tento kurz vysvětluje, jak se připojit k uzlu hardware a nastavení hesla. 

V tomto kurzu se naučíte, jak: 

> [!div class="checklist"]
> * Připojte klávesnici a monitor k uzlu a zapnout ho
> * Nastavení hesla pro uživatele, port a kořenové iDRAC v tomto uzlu
> * Přihlaste se jako uživatel root 

Tento postup opakujte pro každý uzel, který budete používat ve vašem clusteru. 

Tento kurz trvá přibližně 15 minut. 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu, proveďte tyto kroky: 

* [Nainstalujte](fxt-install.md) každého uzlu Azure FXT hrany vyfiltrovat v zařízení do racku a připojit napájecích kabelů a přístup k síti, jak je popsáno v [starší kurz](fxt-network-power.md). 
* Najdete klávesnice připojeného přes USB a VGA připojené monitorování, který může připojit k uzlům hardwaru. (Uzlu sériového portu je neaktivní, než nastavíte heslo.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Připojte klávesnici a monitor k uzlu

Fyzicky připojte k uzlu Azure FXT hrany Filer monitorem a klávesnicí. 

* Monitorování připojení k portu VGA.
* Připojte klávesnici na jeden z portů USB. 

Pomocí tohoto odkazu diagramu vyhledejte porty na zadní straně skříni. 

> [!NOTE]
> Po nastavení hesla, je neaktivní až do sériového portu. 

![Diagram zadní Azure FXT hrany Filer se sériovým VGA a portů USB s popiskem](media/fxt-back-serial-vga-usb.png)

Přepínač KVM můžete použít, pokud se chcete připojit víc než jeden uzel na stejné periferní zařízení. 

Napájení uzlu stisknutím tlačítka napájení v popředí. 

![Diagram front Azure FXT hrany Filer - round tlačítko napájení je označené jako nahoře vpravo](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Nastavení počátečního hesla 

Uzel Azure FXT hrany Filer vytiskne různé zprávy pro monitorování při spuštění. Po chvíli se zobrazí obrazovky počátečním nastavení následujícím způsobem:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

Heslo, které zadáte, se používá pro dvě věci: 

* Je dočasný kořenové heslo pro tento uzel Azure FXT hrany vyfiltrovat. 

  Toto heslo se změní při vytváření clusteru pomocí tohoto uzlu, nebo při přidávání tohoto uzlu do clusteru. Heslo pro správu clusteru (přidružené k uživateli ``admin``) je také kořenové heslo pro všechny uzly v clusteru.

* Je dlouhodobá heslo iDRAC/IPMI hardwaru portu pro správu.

  Zajistěte, aby že si zapamatovat heslo v případě, že budete muset přihlásit se přes rozhraní IPMI později Poradce při potížích s hardwarem.

Zadejte a potvrďte heslo: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Po zadání hesla, systém bude pokračovat, spouštění. Po dokončení, poskytuje ``login:`` řádku. 

## <a name="sign-in-as-root"></a>Přihlaste se jako uživatel root

Přihlaste se jako ``root`` s heslem, které jste právě nastavili. 

```
login: root
Password:**********
```

Po přihlášení jako uživatel root síťové porty jsou aktivní a bude kontaktovat DHCP server pro IP adresy. 

## <a name="next-steps"></a>Další postup

Uzel je připravené k odeslání nepatří do clusteru. Slouží k vytvoření clusteru Azure FXT hrany vyfiltrovat, nebo se dají [přidat do existujícího clusteru](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Vytvoření clusteru](fxt-cluster-create.md)
