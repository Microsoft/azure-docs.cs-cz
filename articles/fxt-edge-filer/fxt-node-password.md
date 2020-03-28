---
title: 'Kurz: Inicializovat hardware – Azure FXT Edge Filer'
description: Jak nastavit počáteční heslo na uzlech Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8cb5f639deb0630575c46db30efe70ad967324a8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550887"
---
# <a name="tutorial-set-hardware-passwords"></a>Kurz: Nastavení hardwarových hesel

Při prvním zapnutí uzlu Azure FXT Edge Filer je nutné nastavit kořenové heslo. Hardwarové uzly nejsou dodávány s výchozím heslem. 

Síťové porty jsou zakázány, dokud není nastaveno heslo a uživatel kořenového nastavení se přihlásí.

Tento krok proveďte po instalaci a kabeláž uzlu, ale před pokusem o vytvoření clusteru. 

Tento kurz vysvětluje, jak se připojit k hardwarovému uzlu a nastavit heslo. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Připojte klávesnici a monitor k uzlu a zapnute jej
> * Nastavení hesel pro port iDRAC a uživatele root v tomto uzlu
> * Přihlásit se jako root 

Opakujte tyto kroky pro každý uzel, který budete používat v clusteru. 

Tento kurz trvá přibližně 15 minut. 

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu proveďte tyto kroky: 

* [Nainstalujte](fxt-install.md) každý uzel Azure FXT Edge Filer do racku zařízení a připojte napájecí kabely a přístup k síti, jak je popsáno v [předchozím kurzu](fxt-network-power.md). 
* Najděte klávesnici připojenou přes USB a monitor připojený k VGA, který můžete připojit k hardwarovým uzlům. (Sériový port uzlu je před nastavením hesla neaktivní.)

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Připojení klávesnice a monitoru k uzlu

Fyzicky připojte monitor a klávesnici k uzlu Azure FXT Edge Filer. 

* Připojte monitor k portu VGA.
* Připojte klávesnici k jednomu z portů USB. 

Tento referenční diagram slouží k vyhledání portů na zadní straně šasi. 

> [!NOTE]
> Sériový port je neaktivní až po nastavení hesla. 

![diagram zadní části aplikace Azure FXT Edge Filer se sériovými porty, porty VGA a USB označenými](media/fxt-back-serial-vga-usb.png)

Přepínač KVM můžete použít, pokud chcete připojit více než jeden uzel ke stejným periferiím. 

Zapněte uzel stisknutím tlačítka napájení na přední straně. 

![diagram přední části Azure FXT Edge Filer - kulaté tlačítko napájení je označeno vpravo nahoře](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Nastavení počátečních hesel 

Uzel Azure FXT Edge Filer bude při zavádění tisknout různé zprávy na monitor. Po několika okamžicích se zobrazí úvodní obrazovka nastavení, jako je tato:

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

Zadejte heslo, které zadáte, pro dvě věci: 

* Jedná se o dočasné kořenové heslo pro tento uzel Azure FXT Edge Filer. 

  Toto heslo se změní při vytvoření clusteru pomocí tohoto uzlu nebo při přidání tohoto uzlu do clusteru. Heslo pro správu clusteru ``admin``(přidružené k uživateli) je také kořenové heslo pro všechny uzly v clusteru.

* Jedná se o dlouhodobé heslo pro port pro správu hardwaru iDRAC/IPMI.

  Ujistěte se, že si pamatujete heslo v případě, že se budete muset později přihlásit pomocí ipmi, abyste se zabývali problémem s hardwarem.

Zadejte a potvrďte heslo: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Po zadání hesla systém pokračuje v zavádění. Když to skončí, dává ``login:`` výzvu. 

## <a name="sign-in-as-root"></a>Přihlásit se jako root

Přihlaste ``root`` se jako heslo, které jste právě nastavili. 

```
login: root
Password:**********
```

Po přihlášení jako root jsou síťové porty aktivní a budou kontaktovat server DHCP pro adresy IP. 

## <a name="next-steps"></a>Další kroky

Uzel je připraven být součástí clusteru. Můžete ji použít k vytvoření clusteru Azure FXT Edge Filer, nebo ho můžete [přidat do existujícího clusteru](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Vytvoření clusteru](fxt-cluster-create.md)
