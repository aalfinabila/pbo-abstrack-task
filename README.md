using System;
using System.Collections.Generic;

public interface IKemampuan
{
    void Gunakan(Robot target);
    int Cooldown { get; set; }
}


public abstract class Robot
{
    public string Nama { get; set; }
    public int Energi { get; set; }
    public int Armor { get; set; }
    public int Serangan { get; set; }

    public Robot(string nama, int energi, int armor, int serangan)
    {
        Nama = nama;
        Energi = energi;
        Armor = armor;
        Serangan = serangan;
    }

    
    public void Serang(Robot target)
    {
        Console.WriteLine($"{Nama} menyerang {target.Nama}!");
        int damage = Serangan - target.Armor;
        if (damage < 0) damage = 0;
        target.Energi -= damage;
        Console.WriteLine($"{target.Nama} kehilangan {damage} energi. Energi tersisa: {target.Energi}");
    }

   
    public abstract void GunakanKemampuan(IKemampuan kemampuan, Robot target);

    
    public void CetakInformasi()
    {
        Console.WriteLine($"Nama: {Nama}, Energi: {Energi}, Armor: {Armor}, Serangan: {Serangan}");
    }
}


public class BosRobot : Robot
{
    public int Pertahanan { get; set; }

    public BosRobot(string nama, int energi, int armor, int serangan, int pertahanan)
        : base(nama, energi, armor, serangan)
    {
        Pertahanan = pertahanan;
    }

    public override void GunakanKemampuan(IKemampuan kemampuan, Robot target)
    {
        Console.WriteLine($"{Nama} menggunakan kemampuan spesial!");
        kemampuan.Gunakan(target);
    }

    public void Diserang(Robot penyerang)
    {
        Console.WriteLine($"{Nama} menerima serangan dari {penyerang.Nama}");
        int damage = penyerang.Serangan - Pertahanan;
        if (damage < 0) damage = 0;
        Energi -= damage;
        Console.WriteLine($"{Nama} kehilangan {damage} energi. Energi tersisa: {Energi}");
    }

    public void Mati()
    {
        if (Energi <= 0)
        {
            Console.WriteLine($"{Nama} telah mati!");
        }
    }
}


public class Perbaikan : IKemampuan
{
    public int Cooldown { get; set; }

    public void Gunakan(Robot target)
    {
        int restoreEnergy = 20;
        target.Energi += restoreEnergy;
        Console.WriteLine($"{target.Nama} menggunakan Perbaikan. Energi dipulihkan sebanyak {restoreEnergy}. Energi sekarang: {target.Energi}");
    }
}

public class SeranganListrik : IKemampuan
{
    public int Cooldown { get; set; }

    public void Gunakan(Robot target)
    {
        int damage = 30;
        target.Energi -= damage;
        Console.WriteLine($"{target.Nama} terkena Serangan Listrik, kehilangan {damage} energi. Energi sekarang: {target.Energi}");
    }
}

public class SeranganPlasma : IKemampuan
{
    public int Cooldown { get; set; }

    public void Gunakan(Robot target)
    {
        int damage = 40;
        target.Armor -= 10;
        if (target.Armor < 0) target.Armor = 0;
        target.Energi -= damage;
        Console.WriteLine($"{target.Nama} terkena Serangan Plasma, kehilangan {damage} energi dan 10 armor. Energi sekarang: {target.Energi}, Armor sekarang: {target.Armor}");
    }
}

public class PertahananSuper : IKemampuan
{
    public int Cooldown { get; set; }

    public void Gunakan(Robot target)
    {
        target.Armor += 15;
        Console.WriteLine($"{target.Nama} menggunakan Pertahanan Super. Armor meningkat menjadi {target.Armor}.");
    }
}


public class RobotTempur : Robot
{
    public RobotTempur(string nama, int energi, int armor, int serangan)
        : base(nama, energi, armor, serangan) { }

    public override void GunakanKemampuan(IKemampuan kemampuan, Robot target)
    {
        Console.WriteLine($"{Nama} menggunakan kemampuan spesial!");
        kemampuan.Gunakan(target);
    }
}


class Program
{
    static void Main(string[] args)
    {
        
        Robot robotA = new RobotTempur("Robot A", 100, 20, 30);
        Robot robotB = new RobotTempur("Robot B", 90, 25, 25);
        BosRobot bosRobot = new BosRobot("Boss Robot", 200, 30, 50, 40);

        
        IKemampuan perbaikan = new Perbaikan();
        IKemampuan seranganListrik = new SeranganListrik();
        IKemampuan seranganPlasma = new SeranganPlasma();
        IKemampuan pertahananSuper = new PertahananSuper();

        
        robotA.CetakInformasi();
        robotB.CetakInformasi();
        bosRobot.CetakInformasi();

      
        robotA.Serang(robotB);
        robotB.GunakanKemampuan(seranganListrik, robotA);
        bosRobot.Diserang(robotA);
        bosRobot.GunakanKemampuan(pertahananSuper, bosRobot);

     
    }
}
