

Když vytvoříte projekt webové aplikace pro Azure, můžete zřídit virtuální počítač v Azure. Potom můžete nakonfigurovat virtuální počítač s další software, nebo použít virtuální počítač pro účely ladění a diagnostiky.

Pokud chcete vytvořit virtuální počítač při vytvoření webové aplikace, postupujte takto:

1. V sadě Visual Studio, klikněte na tlačítko **soubor** > **nový** > **projektu** > **webové**a potom vyberte **Webové aplikace ASP.NET** (v části **Visual C#** nebo **jazyka Visual Basic** uzly).
2. V **nový projekt ASP.NET** dialogové okno, vyberte typ webové aplikace, které chcete a v části Azure v dialogovém okně (v pravém dolním rohu) ujistěte se, že **hostitel v cloudu** zaškrtávací políčko je vybrané (Toto zaškrtávací políčko je označeno **vytvořit vzdálené prostředky** některé instalace).
   
    ![][0]
3. V tomto příkladu v rozevíracím seznamu v části Microsoft Azure, zvolte **virtuálního počítače (v1)**a klikněte **OK** tlačítko.
4. Pokud se zobrazí výzva, přihlaste se k Azure. **Vytvořit virtuální počítač** zobrazí se dialogové okno.
   
    ![][2]
5. V **název DNS** pole, zadejte název pro virtuální počítač. Název DNS musí být jedinečný v Azure. Pokud zadaný název není k dispozici, se zobrazí červený vykřičník.
6. V **Image** vyberte bitovou kopii chcete založit na virtuální počítač. Můžete použít všechny bitové kopie standardní virtuální počítač Azure nebo vaší bitové kopie, které jste odeslali do Azure.
7. Ponechte **povolit IIS a Web Deploy** zaškrtávací políčko Vybrat, pokud chcete nainstalovat na jiný webový server. Nebudete moci publikovat ze sady Visual Studio, pokud zakážete, nasazení webu. Službu IIS a Web Deploy můžete přidat do jakéhokoli z zabalené Image systému Windows Server, včetně vlastních bitových kopií.
8. V **velikost** seznamu, zvolte velikost virtuálního počítače.
9. Zadejte přihlašovací pověření pro tento virtuální počítač. Poznamenejte si z nich, protože budete potřebovat jejich přístupu k počítači pomocí vzdálené plochy.
10. V **umístění** seznamu, vyberte oblast pro hostování tohoto virtuálního počítače.
11. Klikněte **OK** tlačítko start, vytvoření virtuálního počítače. Můžete sledovat průběh operace v **výstup** okno.
    
    ![][3]
12. Při zřízení virtuálního počítače, ve vytvoří publikovaná skripty **PublishScripts** uzlu ve vašem řešení. Publikované skript spustí a zřídí virtuálního počítače v Azure. **Výstup** okno zobrazuje stav. Skript provede následující akce nastavit virtuální počítač:
    
    * Pokud ještě neexistuje, vytvoří virtuální počítač.
    * Vytvoří účet úložiště s názvem, který začíná `devtest`, ale jenom v případě, že v zadané oblasti není již účtu úložiště.
    * Vytvoří cloudovou službu jako kontejner pro virtuální počítač a vytvoří webové role pro webovou aplikaci.
    * Nakonfiguruje Web Deploy na virtuálním počítači.
    * Nakonfiguruje službu IIS a ASP.NET na virtuálním počítači.
    
    ![][4]
13. (Volitelné) Můžete připojit k novému virtuálnímu počítači. V **Průzkumníka serveru**, rozbalte **virtuální počítače** uzlu, zvolte uzel pro virtuální počítač, který jste vytvořili a v jeho místní nabídce a potom vyberte **připojit pomocí vzdálené plochy**. Můžete taky v **Průzkumník cloudu** můžete **otevřít na portálu** v místní nabídce a připojte se k virtuálnímu počítači existuje.
    
    ![][5]

## <a name="next-steps"></a>Další kroky
Pokud chcete přizpůsobit publikované skripty, které jste vytvořili, přečtěte si podrobné informace v [pomocí skriptů prostředí PowerShell systému Windows k publikování pro vývoj a testovací prostředí](http://msdn.microsoft.com/library/dn642480.aspx).

[0]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_NewProject.PNG
[1]: ./media/dotnet-visual-studio-create-virtual-machine/CreateVM_SignIn.PNG
[2]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_CreateVM.PNG
[3]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_Provisioning.png
[4]: ./media/virtual-machines-common-classic-web-app-visual-studio/CreateVM_SolutionExplorer.png
[5]: ./media/virtual-machines-common-classic-web-app-visual-studio/VS_Create_VM_Connect.png
