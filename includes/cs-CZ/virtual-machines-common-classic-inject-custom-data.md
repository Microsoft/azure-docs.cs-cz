


Toto téma popisuje postup:

* Vložení dat do Azure virtuální počítač (VM) při jeho zřizování.
* Načíst ho pro systém Windows a Linux.
* Použijte speciální nástrojů dostupných v některé systémy ke zjišťování a automaticky zpracovávat vlastní data.

> [!NOTE]
> Tento článek popisuje, jak vlastních dat může pomocí virtuálního počítače vytvořené pomocí Azure Service Management API vložit. Jak používat rozhraní API pro správu prostředků Azure, najdete v sekci [příklad šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-customdata).
> 
> 

## <a name="injecting-custom-data-into-your-azure-virtual-machine"></a>Vložení vlastní data do virtuálního počítače Azure
Tato funkce v současné době podporuje pouze v [rozhraní příkazového řádku Azure](https://github.com/Azure/azure-xplat-cli). Tady vytváříme `custom-data.txt` soubor, který obsahuje naše data pak vložit v k virtuálnímu počítači při zřizování. I když můžete použít libovolnou z možností pro `azure vm create` příkaz následující ukazuje jeden ze způsobů velmi základní:

```
    azure vm create <vmname> <vmimage> <username> <password> \  
    --location "West US" --ssh 22 \  
    --custom-data ./custom-data.txt  
```


## <a name="using-custom-data-in-the-virtual-machine"></a>Pomocí vlastních dat ve virtuálním počítači.
* Pokud je virtuální počítač Azure virtuálních počítačů na bázi Windows, pak uložení souboru vlastních dat do `%SYSTEMDRIVE%\AzureData\CustomData.bin`. I když byl kódováním Base 64 přenos z místního počítače do nového virtuálního počítače, je automaticky dekódované a může být otevřít nebo použít okamžitě.
  
  > [!NOTE]
  > Pokud soubor existuje, se přepíše. Zabezpečení v adresáři je nastaveno na **systému: Úplné řízení** a **Administrators: Úplné řízení**.
  > 
  > 
* Pokud virtuální počítač Azure je počítač se systémem Linux, budou v jednom z následujících míst v závislosti na vaší distro umístěny vlastní datový soubor. Data mohou být kódováním base64, takže je třeba nejprve dekódování dat:
  
  * `/var/lib/waagent/ovf-env.xml`
  * `/var/lib/waagent/CustomData`
  * `/var/lib/cloud/instance/user-data.txt` 

## <a name="cloud-init-on-azure"></a>Init cloudu v Azure
Pokud virtuální počítač Azure z image Ubuntu nebo CoreOS CustomData můžete použít k odeslání do cloudu init cloudu config. Nebo pokud vlastní datový soubor skriptu, pak cloudu init jednoduše ho provést.

### <a name="ubuntu-cloud-images"></a>Ubuntu cloudu obrázků
Ve většině Image Azure Linux, by upravit "/ etc/waagent.conf" ke konfiguraci disku dočasných prostředků a odkládacího souboru. V tématu [Azure Linux Agent uživatelská příručka](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Další informace.

Však do bitové kopie Ubuntu cloudu, je potřeba použít cloudové init nakonfigurovat disk prostředků (tedy "dočasné" disk) a Prohodit oddílu. Na stránkách wiki Ubuntu další podrobnosti najdete na následující stránce: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps-using-cloud-init"></a>Další kroky: pomocí init cloudu
Další informace najdete v tématu [cloudu init dokumentaci Ubuntu](https://help.ubuntu.com/community/CloudInit).

<!--Link references-->
[Přidání Role referenční dokumentace rozhraní API REST služby správy](http://msdn.microsoft.com/library/azure/jj157186.aspx)

[Rozhraní příkazového řádku Azure](https://github.com/Azure/azure-xplat-cli)

