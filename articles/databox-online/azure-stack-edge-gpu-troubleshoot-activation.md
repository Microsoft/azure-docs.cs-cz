---
title: Použití Azure Portal k řešení chyb aktivace souvisejících s Azure Stack Edge pro s grafickým procesorem | Microsoft Docs
description: Popisuje, jak řešit potíže s Azure Stackm při aktivaci GPU pro procesor a s problémy souvisejícími s trezorem klíčů.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442141"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Řešení potíží s aktivací na zařízení GPU Azure Stack Edge pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak řešit problémy s aktivací na zařízení GPU Azure Stack Edge pro. 


## <a name="activation-errors"></a>Chyby aktivace

Následující tabulka shrnuje chyby týkající se aktivace zařízení a odpovídající doporučené řešení.

| Chybová zpráva| Doporučené řešení |
|------------------------------------------------------|--------------------------------------|
| Pokud se před aktivací zařízení s aktivačním klíčem odstraní Azure Key Vault, který se používá pro aktivaci, zobrazí se tato chyba. <br> ![Chyba trezoru klíčů 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Pokud je Trezor klíčů odstraněný, můžete Trezor klíčů obnovit, pokud je trezor v době trvání ochrany vyprázdnění. Postupujte podle kroků v části [obnovení trezoru klíčů](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). <br>Pokud uplynula doba trvání ochrany vyprázdnění, Trezor klíčů nelze obnovit. O dalších krocích se poraďte s podporou Microsoftu. |
| Pokud se Azure Key Vault po aktivaci zařízení odstraní a pak se pokusíte provést jakoukoli operaci, která zahrnuje šifrování, například: **Přidat uživatele**, **Přidat sdílenou složku**, **nakonfigurovat výpočetní** prostředky, zobrazí se tato chyba. <br> ![Chyba trezoru klíčů 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Pokud je Trezor klíčů odstraněný, můžete Trezor klíčů obnovit, pokud je trezor v době trvání ochrany vyprázdnění. Postupujte podle kroků v části obnovení trezoru klíčů. <br>Pokud uplynula doba trvání ochrany vyprázdnění, Trezor klíčů nelze obnovit. O dalších krocích se poraďte s podporou Microsoftu. |
| Pokud se klíč integrity kanálu v Azure Key Vault odstraní a Vy a pak se pokusíte provést nějaké operace, které zahrnují šifrování, například: **Přidat uživatele**, **Přidat sdílenou složku**, **nakonfigurovat výpočetní** prostředky, zobrazí se tato chyba. <br> ![Chyba trezoru klíčů 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Pokud se klíč integrity kanálu v trezoru klíčů odstraní, ale pořád se v době trvání vyprázdnění, postupujte podle kroků v části [zrušení odebrání klíče trezoru klíčů](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Pokud doba trvání ochrany vypršela a pokud máte klíč zálohovaný, můžete obnovit ze zálohy, jinak nemůžete tento klíč obnovit. O dalších krocích se poraďte s podporou Microsoftu. |
| Pokud se generování aktivačního klíče z důvodu chyby nepovede, zobrazí se tato chyba. Další podrobnosti jsou k dispozici v oznámení. <br> ![Chyba trezoru klíčů 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Zajistěte, aby byly porty a adresy URL zadané v [přístupu Azure Key Vault za bránou firewall](../key-vault/general/access-behind-firewall.md) otevřené v bráně firewall, aby bylo možné získat přístup k Key Vault. Počkejte pár minut a zkuste operaci zopakovat. Pokud potíže potrvají, obraťte se na podpora Microsoftu. |
| Pokud má uživatel oprávnění jen pro čtení, není povoleno vygenerovat aktivační klíč a zobrazí se tato chyba. <br> ![Chyba trezoru klíčů 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Důvodem může být to, že nemáte správný přístup nebo že není zaregistrován  *Microsoft. webtrezor* .<li>Ujistěte se, že máte přístup vlastníka nebo přispěvatele na úrovni skupiny prostředků používané pro prostředek Azure Stack Edge.</li><li>Ujistěte se, že je zaregistrován poskytovatel prostředků Microsoft. klíčů. Pokud chcete zaregistrovat poskytovatele prostředků, přečtěte si předplatné používané pro Azure Stack hraničních prostředků. Přejít na **poskytovatele prostředků**, vyhledejte *Microsoft.* klíčů a vyberte a **Zaregistrujte**.</li> |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [řešení problémů se zařízením](azure-stack-edge-gpu-troubleshoot.md).
