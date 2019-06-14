---
title: Výměna kontroleru StorSimple 8600 EBOD | Dokumentace Microsoftu
description: Vysvětluje, jak odstranit a nahradit jeden nebo oba řadiče EBOD zařízení StorSimple 8600.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578666"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Výměna kontroleru EBOD, zařízení StorSimple

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak nahradit vadným modul řadiče EBOD na zařízení s Microsoft Azure StorSimple. Nahradit modul řadiče EBOD, budete muset:

* Odeberte chybné kontroleru EBOD
* Instalaci nového řadiče EBOD

Před zahájením vezměte v úvahu následující informace:

* Prázdné EBOD moduly musí být vložen do všechny nepoužívané sloty. Přílohu nebude studenou správně, pokud slot je otevřená.
* Kontroleru EBOD je za provozu a může být odebraly nebo nahradily. Neodebírejte modulu se nezdařilo, dokud nezískáte náhradu. Když zahájíte proces nahrazení, se musí dokončit během 10 minut.

> [!IMPORTANT]
> Než se pokusíte odebrat nebo nahradit jakoukoli komponentu StorSimple, ujistěte se, abyste si [bezpečnosti ikonu konvence](storsimple-safety.md#safety-icon-conventions) a dalších [bezpečnostní opatření](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Odeberte kontroleru EBOD
Před nahrazením modulu se nezdařilo řadiče EBOD v zařízení StorSimple, ujistěte se, že ostatní moduly řadiče EBOD je aktivní a v chodu. Následující postup a tabulka vysvětlují, jak modul řadiče EBOD odebrat.

#### <a name="to-remove-an-ebod-module"></a>Chcete-li odebrat modul EBOD
1. Otevřete web Azure Portal.
2. Přejděte do svého zařízení a přejděte do **nastavení** > **stav hardwaru**a ověřte, zda je stav LED pro modul active řadiče EBOD zelené a LED selhání kontroleru EBOD modul je červený.
3. Vyhledejte modul selhání kontroleru EBOD zádi zařízení.
4. Odeberte kabely, které modul řadiče EBOD ke kontroleru připojit před přepnutím modulu EBOD mimo systém.
5. Poznamenejte si přesný portů SAS modulu řadiče EBOD, který byl připojen k řadiči. Budete muset obnovit systém tuto konfiguraci po nahrazení EBOD modulu.
   
   > [!NOTE]
   > Obvykle to bude Port A, který je označen jako **hostovat v** v následujícím diagramu.
   
    ![Kontroleru EBOD propojovací rozhraní systému](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Obrázek 1** EBOD zpět z modulu
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Selhání LED |
   | 2 |Napájení LED |
   | 3 |SAS konektory |
   | 4 |SAS indikátorů LED |
   | 5 |Sériové porty pouze pro objekt pro vytváření |
   | 6 |Port (hostitele v) |
   | 7 |Port B (hostitel out) |
   | 8 |Port C (pouze pro objekt pro vytváření použití) |

## <a name="install-a-new-ebod-controller"></a>Instalaci nového řadiče EBOD
Následující postup a tabulka vysvětlují, jak nainstalovat modul řadiče EBOD v zařízení StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Instalace řadiče EBOD
1. Zkontrolujte zařízení EBOD poškození, zejména rozhraní konektoru. Pokud jsou všechny kódy PIN ohnuty instalaci nového řadiče EBOD.
2. Se zámky na pozici, otevřít potáhněte modulu do prostoru dokud zapojit zámků.
   
    ![Instalace řadiče EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Obrázek 2** instaluje se modul kontroleru EBOD
3. Zavřete zámek. Kliknutím by měl poslechněte si, jak zaujme zámek.
   
    ![Uvolnění EBOD západky](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Obrázek 3** zavření západku modulu EBOD
4. Znovu připojte kabely. Použijte přesnou konfiguraci, která byla k dispozici před nahrazení. Viz následující diagram a tabulka podrobné informace o tom, jak připojit kabely.
   
    ![Zapojení kabeláže zařízení 4U výkon](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Obrázek 4**. Opětovné připojení kabely
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Primární zařízení |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontroler 0 |
   | 5 |Kontroler 1 |
   | 6 |Kontroleru EBOD 0 |
   | 7 |Kontroleru EBOD 1 |
   | 8 |Skříň EBOD |
   | 9 |Jednotek pro distribuci napájení |

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).

