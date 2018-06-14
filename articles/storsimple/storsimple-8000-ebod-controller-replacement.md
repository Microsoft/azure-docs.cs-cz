---
title: Nahraďte řadič StorSimple 8600 EBOD | Microsoft Docs
description: Vysvětluje, jak odeberete a nahradíte jeden nebo oba řadiče EBOD na zařízení StorSimple 8600.
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
ms.openlocfilehash: 45699c267d1009c4884dd164fd3f2950d6d5f555
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927576"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Nahraďte řadič EBOD zařízení StorSimple

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak nahradit vadný modul řadiče EBOD na zařízení s Microsoft Azure StorSimple. Chcete-li nahradit modul EBOD řadiče, je potřeba:

* Odebrat vadný řadič EBOD
* Nainstalovat nový řadič EBOD

Před zahájením vezměte v úvahu následující informace:

* Prázdné EBOD moduly musí být vložený do všechny nepoužívané sloty. Skříni nebude cool správně, pokud slotu je ponechány otevřené.
* Řadič EBOD je za provozu a můžete ho odebrat nebo nahradit. Neodebírejte selhání modulu, dokud nebudete mít nahrazení. Když zahájíte proces nahrazení, musí ji dokončíte během 10 minut.

> [!IMPORTANT]
> Před pokusem o odeberte nebo nahraďte všechny součásti, StorSimple, ujistěte se, abyste si prošli [zabezpečení ikonu konvence](storsimple-safety.md#safety-icon-conventions) a dalších [bezpečnostní opatření](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Odebrat řadič EBOD
Před výměnou selhání modulu EBOD řadiče v zařízení StorSimple, ujistěte se, zda jiné řadiče modul EBOD je aktivní a spuštěná. Následující postup a tabulka vysvětlují, jak odebrat modul EBOD řadiče.

#### <a name="to-remove-an-ebod-module"></a>Chcete-li odebrat modul EBOD
1. Otevřete portál Azure.
2. Přejděte na zařízení a přejděte do **nastavení** > **stavu hardwaru**a ověřte, zda je stav DIODU pro modul active řadiče EBOD zelená a LED selhání EBOD kontroleru modul je red.
3. Vyhledejte modul selhání řadiče EBOD zadní straně zařízení.
4. Odeberte kabely, které modul řadiče EBOD připojení k řadiči před přepnutím modul EBOD mimo systém.
5. Poznamenejte si přesný SAS portu EBOD řadiče modul, který byl připojený k řadiči. Budete muset po replace modul EBOD obnovení systému do této konfigurace.
   
   > [!NOTE]
   > Obvykle to bude Port A, který je označený jako **hostitele v** v následujícím diagramu.
   
    ![Propojovací rozhraní EBOD řadiče](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Obrázek 1** zpět of EBOD modulu
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Selhání DIODU |
   | 2 |Napájení DIODU |
   | 3 |SAS konektory |
   | 4 |LED SAS |
   | 5 |Sériové porty pouze pro objekt pro vytváření |
   | 6 |Port (hostitel v) |
   | 7 |Port B (hostitel out) |
   | 8 |Port C (pouze pro objekt pro vytváření použití) |

## <a name="install-a-new-ebod-controller"></a>Nainstalovat nový řadič EBOD
Následující postup a tabulka vysvětlují, jak nainstalovat modul EBOD řadiče v zařízení StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Abyste mohli nainstalovat řadič EBOD
1. Zkontrolujte zařízení EBOD škody, zejména konektor rozhraní. Neinstalujte nový řadič EBOD, pokud jsou ohnuty všechny kódy PIN.
2. S zámky v otevřené poloze posuňte modul do skříni, dokud západek zapojení.
   
    ![Instalaci EBOD řadiče](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Obrázek 2** instalaci modulu EBOD řadiče
3. Zavřete zámek. Klikněte na měli vědět, jak zapojí zámek.
   
    ![Uvolnění EBOD západky](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Obrázek 3** zavření západky modulu EBOD
4. Znovu připojte kabely. Použijte přesnou konfiguraci, která se nachází před nahrazení. Viz následující diagram a tabulka Podrobnosti o tom, jak připojte kabely.
   
    ![Zapojení kabeláže zařízení 4U pro napájení](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Obrázek 4**. Opětovné připojení kabely
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |Primární skříň |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Řadič 0 |
   | 5 |Řadič 1 |
   | 6 |EBOD řadič 0 |
   | 7 |EBOD řadiči 1 |
   | 8 |EBOD skříň |
   | 9 |Jednotek pro distribuci napájení |

## <a name="next-steps"></a>Další kroky
Další informace o [StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).

