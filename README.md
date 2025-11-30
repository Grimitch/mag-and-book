using System;
using System.Collections.Generic;
using System.Linq;

/* ========= INTERFACES ========= */

public interface ISpell : IComparable<ISpell>
{
    void Cast();
    int GetPower();
}

public interface IDarkMagic { }

/* ========= SPELL IMPLEMENTATIONS ========= */

public class Fireball : ISpell
{
    public int Power { get; set; }

    public Fireball() { Power = 50; }

    public void Cast() => 
        Console.WriteLine($"🔥 Fireball explodes with power {Power}!");

    public int GetPower() => Power;

    public int CompareTo(ISpell other) => Power.CompareTo(other.GetPower());
}

public class HealingWave : ISpell
{
    public int Power { get; set; }

    public HealingWave() { Power = 30; }

    public void Cast() =>
        Console.WriteLine($"💚 Healing wave restores {Power} HP!");

    public int GetPower() => Power;

    public int CompareTo(ISpell other) => Power.CompareTo(other.GetPower());
}

public class AvadaKedavra : ISpell, IDarkMagic
{
    public int Power { get; set; }

    public AvadaKedavra() { Power = 100; }

    public void Cast() =>
        Console.WriteLine($"🖤 Avada Kedavra — instant kill! Power {Power}");

    public int GetPower() => Power;

    public int CompareTo(ISpell other) => Power.CompareTo(other.GetPower());
}

/* ========= GENERIC SPELLBOOK ========= */

public class Spellbook<T> where T : ISpell, new(), IComparable<ISpell>
{
    private List<T> spells = new List<T>();

    public void LearnSpell(T spell)
    {
        if (spells.Any(s => s.CompareTo(spell) == 0))
        {
            Console.WriteLine("❗Таке закляття вже є в книзі!");
            return;
        }

        spells.Add(spell);
        Console.WriteLine($"📘 Вивчено: {spell.GetType().Name} [{spell.GetPower()}]");
    }

    public void SortByPower() =>
        spells = spells.OrderByDescending(s => s.GetPower()).ToList();

    public void CastStrongest()
    {
        if (spells.Count == 0)
        {
            Console.WriteLine("Книга порожня!");
            return;
        }

        Console.Write(" Найсильніше закляття → ");
        spells.First().Cast();
    }

    public void InvokeRitual() where T : class, IDarkMagic
    {
        if (!spells.All(s => s is IDarkMagic))
            throw new InvalidOperationException(" Ритуал можливий ТІЛЬКИ для темних заклять!");

        Console.WriteLine(" Темний ритуал розпочато...");
        foreach (var spell in spells) spell.Cast();
    }
}

public class Program
{
    public static void Main()
    {
        Console.WriteLine("==== ЗВИЧАЙНА КНИГА ====\n");

        var book = new Spellbook<ISpell>();

        book.LearnSpell(new Fireball { Power = 60 });
        book.LearnSpell(new HealingWave { Power = 25 });
        book.LearnSpell(new Fireball { Power = 45 });
        book.LearnSpell(new HealingWave { Power = 50 });
        book.LearnSpell(new Fireball { Power = 75 });

        book.SortByPower();
        book.CastStrongest();

        Console.WriteLine("\n==== ТЕМНА КНИГА ====\n");

        var darkBook = new Spellbook<ISpell>();

        darkBook.LearnSpell(new AvadaKedavra { Power = 120 });
        darkBook.LearnSpell(new AvadaKedavra { Power = 110 });
        darkBook.LearnSpell(new Fireball()); // МІШАНИЙ ТИП — буде помилка на ритуалі

        try
        {
            darkBook.InvokeRitual(); 
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
}
