---
title: Přizpůsobení vlastností protokolu RDP pomocí prostředí PowerShell – Azure
description: Postup přizpůsobení vlastností protokolu RDP pro virtuální počítače s Windows pomocí rutin prostředí PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ff3661a7e092fd20207fe2e973afc316b2c244ef
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447093"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) pro fond hostitelů

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md).

Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů a přesměrování zvuku, umožňuje poskytovat optimální prostředí pro uživatele podle svých potřeb. Vlastnosti protokolu RDP na virtuálním počítači s Windows můžete přizpůsobit buď pomocí Azure Portal, nebo pomocí parametru *-CustomRdpProperty* v rutině **Update-AzWvdHostPool** .

Úplný seznam podporovaných vlastností a jejich výchozích hodnot najdete v tématu [podporované nastavení souboru RDP](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) .

## <a name="default-rdp-file-properties"></a>Výchozí vlastnosti souboru RDP

Soubory RDP mají ve výchozím nastavení následující vlastnosti:

|Vlastnost RDP|Na ploše|Jako Vzdálená aplikace RemoteApp|
|---|---|---|
|Režim více monitorů|Zakázáno|Povoleno|
|Přesměrování jednotky povolena|Jednotky, schránka, tiskárny, porty COM a čipové karty|Jednotky, schránka a tiskárny|
|Režim vzdáleného zvuku|Přehrát místně|Přehrát místně|

## <a name="prerequisites"></a>Požadavky

Než začnete, postupujte podle pokynů v části [nastavení modulu Azure Virtual Desktop PowerShell](powershell-module.md) a nastavte modul prostředí PowerShell a přihlaste se k Azure.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Konfigurace vlastností protokolu RDP v Azure Portal

Konfigurace vlastností protokolu RDP v Azure Portal:

1. Přihlaste se k Azure na adrese <https://portal.azure.com> .
2. Do panelu hledání zadejte **virtuální klient Windows** .
3. V části služby vyberte **virtuální klient Windows**.
4. Na stránce virtuální počítač s Windows vyberte **fondy hostitelů** v nabídce na levé straně obrazovky.
5. Vyberte **název hostitelského fondu** , který chcete aktualizovat.
6. V nabídce na levé straně obrazovky vyberte **Vlastnosti protokolu RDP** .
7. Nastavte vlastnost, kterou chcete.
   - Alternativně můžete otevřít kartu **Upřesnit** a přidat vlastnosti protokolu RDP ve formátu odděleném středníkem, jako jsou příklady PowerShellu v následujících oddílech.
8. Až budete hotovi, vyberte **Uložit** a uložte provedené změny.

V dalších částech se dozvíte, jak ručně upravit vlastní vlastnosti protokolu RDP v prostředí PowerShell.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Přidat nebo upravit jednu vlastní vlastnost RDP

Pokud chcete přidat nebo upravit jednu vlastní vlastnost RDP, spusťte následující rutinu PowerShellu:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Pokud chcete zjistit, jestli rutina, kterou jste právě spustili, aktualizovala vlastnost, spusťte tuto rutinu:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Pokud jste například kontrolovali vlastnost "audiocapturemode" ve fondu hostitelů s názvem 0301HP, zadáte tuto rutinu:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Přidat nebo upravit více vlastních vlastností protokolu RDP

Chcete-li přidat nebo upravit více vlastních vlastností protokolu RDP, spusťte následující rutiny prostředí PowerShell zadáním vlastních vlastností protokolu RDP jako řetězce odděleného středníkem:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Pomocí následující rutiny můžete ověřit, zda byla vlastnost RDP přidána:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Na základě našeho dřívějšího příkladu rutiny, pokud nastavíte více vlastností protokolu RDP v 0301HP fondu hostitelů, rutina by vypadala takto:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Resetovat všechny vlastní vlastnosti protokolu RDP

Jednotlivé vlastní vlastnosti protokolu RDP můžete obnovit na výchozí hodnoty podle pokynů v tématu [Přidání nebo úprava jedné vlastní vlastnosti protokolu RDP](#add-or-edit-a-single-custom-rdp-property), nebo můžete obnovit všechny vlastní vlastnosti protokolu RDP pro fond hostitelů spuštěním následující rutiny prostředí PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Abyste se ujistili, že jste úspěšně odebrali nastavení, zadejte tuto rutinu:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Další kroky

Teď, když jste přizpůsobili vlastnosti protokolu RDP pro daný fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat je jako součást uživatelské relace. Tyto další návody vám posdělí, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](connect-windows-7-10.md)
- [Připojení k webovému klientovi](connect-web.md)
- [Práce s klientem Android](connect-android.md)
- [Připojení s klientem macOS](connect-macos.md)
- [Připojení s klientem iOS](connect-ios.md)