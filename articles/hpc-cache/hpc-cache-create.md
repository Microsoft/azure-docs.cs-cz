---
title: Vytvoření mezipaměti prostředí Azure HPC
description: Vytvoření instance mezipaměti prostředí Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 894595ee3660532bf046a39e994fa669f7c6b002
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434095"
---
# <a name="create-an-azure-hpc-cache"></a>Vytvoření mezipaměti prostředí Azure HPC

K vytvoření mezipaměti použijte Azure Portal.

![snímek obrazovky s přehledem mezipaměti v Azure Portal s tlačítkem vytvořit v dolní části](media/hpc-cache-home-page.png)

Kliknutím na obrázek níže si můžete přehrát [ukázku](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) pro vytvoření mezipaměti a Přidání cíle úložiště.

[![Miniatura videa: mezipaměť prostředí Azure HPC: Instalační program (kliknutím můžete navštívit stránku video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="define-basic-details"></a>Definovat základní podrobnosti

![snímek stránky s podrobnostmi projektu v Azure Portal](media/hpc-cache-create-basics.png)

V části **Project Details (podrobnosti projektu**) vyberte předplatné a skupinu prostředků, které budou hostovat mezipaměť. Ujistěte se, že je odběr v seznamu [přístupu](hpc-cache-prereqs.md#azure-subscription) .

V části **Podrobnosti o službě**nastavte název mezipaměti a tyto ostatní atributy:

* Umístění – vyberte jednu z [podporovaných oblastí](hpc-cache-overview.md#region-availability).
* Virtuální síť – můžete vybrat existující virtuální síť nebo vytvořit novou.
* Podsíť – vyberte nebo vytvořte podsíť s aspoň 64 IP adresami (/24). Tato podsíť se musí použít jenom pro tuto instanci mezipaměti Azure HPC.

## <a name="set-cache-capacity"></a>Nastavit kapacitu mezipaměti
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stránce **mezipaměť** musíte nastavit kapacitu mezipaměti. Nastavené hodnoty určují, kolik dat vaše mezipaměť může uchovávat a jak rychle může obsluhovat požadavky klientů.

Kapacita má vliv také na náklady na mezipaměť.

Určete kapacitu nastavením těchto dvou hodnot:

* Maximální rychlost přenosu dat pro mezipaměť (propustnost), v GB/s
* Velikost úložiště přidělená pro data uložená v mezipaměti v TB

Vyberte jednu z dostupných hodnot propustnosti a velikosti úložiště mezipaměti.

Pamatujte, že skutečná rychlost přenosu dat závisí na zatížení, rychlosti sítě a typu cílů úložiště. Hodnoty, které zvolíte, nastaví maximální propustnost pro celý systém mezipaměti, ale některé z nich se použijí pro úlohy s režijními náklady. Pokud třeba klient požaduje soubor, který už není uložený v mezipaměti, nebo pokud je soubor označený jako zastaralý, mezipaměť použije určitou propustnost k načtení z back-endu úložiště.

Azure HPC cache spravuje, které soubory jsou uložené do mezipaměti a předem se načítají, aby se maximalizovala míra přístupů do mezipaměti. Obsah mezipaměti se průběžně vyhodnocuje a soubory se přesunou do dlouhodobého úložiště, když se k nim nepoužívá méně často. Vyberte velikost úložiště mezipaměti, která může pohodlně uchovávat aktivní sadu pracovních souborů a navíc další místo pro metadata a další režii.

![snímek stránky s nastavením velikosti mezipaměti](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Povolit šifrování Azure Key Vault (volitelné)

Pokud je vaše mezipaměť v oblasti, která podporuje šifrovací klíče spravované zákazníkem, zobrazí se stránka **klíče šifrování disku** mezi kartami **mezipaměti** a **značky** . V době publikování je tato možnost podporovaná v Východní USA, Střed USA – jih a Západní USA 2.

Pokud chcete spravovat šifrovací klíče používané v úložišti mezipaměti, zadejte Azure Key Vault informace na stránce **klíče pro šifrování disku** . Trezor klíčů musí být ve stejné oblasti a ve stejném předplatném jako mezipaměť.

Pokud nepotřebujete klíče spravované zákazníky, můžete tuto část přeskočit. Azure ve výchozím nastavení šifruje data pomocí klíčů spravovaných Microsoftem. Další informace najdete v tématu [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Po vytvoření mezipaměti nemůžete měnit klíče spravované společností Microsoft ani klíče spravované zákazníkem.
> * Po vytvoření je mezipaměť nutné autorizovat pro přístup k trezoru klíčů. Kliknutím na tlačítko **Povolit šifrování** na stránce **Přehled** mezipaměti zapněte šifrování. Proveďte tento krok během 90 minut od vytvoření mezipaměti.
> * Po této autorizaci se vytvoří disky mezipaměti. To znamená, že počáteční čas vytvoření mezipaměti je krátký, ale mezipaměť nebude připravena k použití po dobu 10 minut nebo více po autorizaci přístupu.

Úplné vysvětlení procesu šifrování klíčů spravovaných zákazníkem najdete v článku [použití šifrovacích klíčů spravovaných zákazníkem pro mezipaměť HPC Azure](customer-keys.md).

![snímek stránky šifrovacích klíčů se zvoleným polem spravované zákazníky a poli trezoru klíčů](media/create-encryption.png)

Vyberte **zákazníka spravované** pro výběr šifrování klíče spravovaného zákazníkem. Zobrazí se pole Specifikace trezoru klíčů. Vyberte Azure Key Vault, který chcete použít, a pak vyberte klíč a verzi, které chcete použít pro tuto mezipaměť. Klíč musí být 2048 klíč RSA. Z této stránky můžete vytvořit nový trezor klíčů, klíč nebo verzi klíče.

Po vytvoření mezipaměti ji musíte autorizovat, aby používala službu trezoru klíčů. Podrobnosti najdete [v tématu autorizace Azure Key Vault šifrování z mezipaměti](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Přidat značky prostředků (volitelné)

Stránka **značky** umožňuje přidat [značky prostředků](https://go.microsoft.com/fwlink/?linkid=873112) do instance mezipaměti prostředí Azure HPC.

## <a name="finish-creating-the-cache"></a>Dokončení vytváření mezipaměti

Po konfiguraci nové mezipaměti klikněte na kartu **Revize + vytvořit** . Portál ověří vaše výběry a umožní vám zkontrolovat své volby. Pokud je vše správné, klikněte na **vytvořit**.

Vytvoření mezipaměti trvá přibližně 10 minut. Průběh můžete sledovat na panelu oznámení Azure Portal.

![snímek obrazovky se stránkou nasazení v mezipaměti a stránkami oznámení na portálu](media/hpc-cache-deploy-status.png)

Po dokončení vytváření se zobrazí oznámení s odkazem na novou instanci mezipaměti Azure HPC a mezipaměť se zobrazí v seznamu **prostředků** vašeho předplatného.

![snímek obrazovky instance mezipaměti HPC Azure v Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Pokud vaše mezipaměť používá šifrovací klíče spravované zákazníkem, může se mezipaměť zobrazit v seznamu prostředků předtím, než se stav nasazení změní na dokončeno. Jakmile je stav mezipaměti **čekat na klíč** , můžete [ho autorizovat](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) k použití trezoru klíčů.

## <a name="next-steps"></a>Další kroky

Po zobrazení mezipaměti v seznamu **prostředky** můžete přejít k dalšímu kroku.

* [Definujte cíle úložiště](hpc-cache-add-storage.md) , které umožní vaší mezipaměti přístup ke zdrojům dat.
* Pokud používáte šifrovací klíče spravované zákazníkem, musíte [autorizovat Azure Key Vault šifrování](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) ze stránky přehled mezipaměti a dokončit tak nastavení mezipaměti. Než budete moct přidat úložiště, musíte provést tento krok. Podrobnosti najdete v tématu [použití šifrovacích klíčů spravovaných zákazníkem](customer-keys.md) .
