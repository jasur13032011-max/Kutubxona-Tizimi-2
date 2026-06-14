# Kutubxona-Tizimi-2
from typing import List


# ==========================
# CUSTOM EXCEPTIONS
# ==========================

class KitobBand(Exception):
    pass


class LimitOshib(Exception):
    pass


# ==========================
# KITOB
# ==========================

class Kitob:

    def __init__(self, sarlavha, muallif, sahifalar):
        self.sarlavha = sarlavha
        self.muallif = muallif
        self.sahifalar = sahifalar
        self.band = False

    @property
    def sahifalar(self):
        return self._sahifalar

    @sahifalar.setter
    def sahifalar(self, value):

        if value <= 0:
            raise ValueError(
                "Sahifalar soni musbat bo'lishi kerak"
            )

        self._sahifalar = value

    def __eq__(self, other):

        if not isinstance(other, Kitob):
            return False

        return (
            self.sarlavha == other.sarlavha
            and
            self.muallif == other.muallif
        )

    def __str__(self):
        return (
            f"{self.sarlavha} - "
            f"{self.muallif}"
        )

    def __repr__(self):
        return (
            f"Kitob("
            f"'{self.sarlavha}', "
            f"'{self.muallif}', "
            f"{self.sahifalar})"
        )


# ==========================
# FOYDALANUVCHI
# ==========================

class Foydalanuvchi:

    MAX_KITOB = 3

    def __init__(self, ism):
        self.ism = ism
        self.olgan_kitoblar = []

    def kitob_olish(self, kitob):

        if len(self.olgan_kitoblar) >= self.MAX_KITOB:
            raise LimitOshib(
                "Kitob limiti oshib ketdi"
            )

        if kitob.band:
            raise KitobBand(
                "Kitob hozir band"
            )

        kitob.band = True
        self.olgan_kitoblar.append(kitob)

    def __str__(self):
        return f"Foydalanuvchi({self.ism})"

    def __repr__(self):
        return (
            f"Foydalanuvchi('{self.ism}')"
        )


# ==========================
# VIP USER
# ==========================

class AVIPUser(Foydalanuvchi):

    MAX_KITOB = 10

    def __init__(self, ism):
        super().__init__(ism)

    def __str__(self):
        return f"VIP({self.ism})"

    def __repr__(self):
        return f"AVIPUser('{self.ism}')"


# ==========================
# KUTUBXONA
# ==========================

class Kutubxona:

    def __init__(self, nom):
        self.nom = nom
        self.kitoblar = []

    def kitob_qoshish(self, kitob):
        self.kitoblar.append(kitob)

    @property
    def mavjud_kitoblar(self):

        return [
            k
            for k in self.kitoblar
            if not k.band
        ]

    @property
    def bandilik_foizi(self):

        if len(self.kitoblar) == 0:
            return 0

        bandlar = len([
            k
            for k in self.kitoblar
            if k.band
        ])

        return round(
            bandlar /
            len(self.kitoblar) * 100,
            2
        )

    def __len__(self):
        return len(self.kitoblar)

    def __iter__(self):
        return iter(self.kitoblar)

    def __contains__(self, item):
        return item in self.kitoblar

    def __getitem__(self, index):
        return self.kitoblar[index]

    def __str__(self):
        return (
            f"{self.nom} "
            f"({len(self.kitoblar)} kitob)"
        )

    def __repr__(self):
        return (
            f"Kutubxona('{self.nom}')"
        )


# ==========================
# DEMO
# ==========================

if __name__ == "__main__":

    k1 = Kitob(
        "Python",
        "Aliyev",
        500
    )

    k2 = Kitob(
        "Django",
        "Valiyev",
        350
    )

    k3 = Kitob(
        "Flask",
        "Karimov",
        280
    )

    k4 = Kitob(
        "FastAPI",
        "Karimov",
        250
    )

    kutubxona = Kutubxona(
        "Markaziy kutubxona"
    )

    kutubxona.kitob_qoshish(k1)
    kutubxona.kitob_qoshish(k2)
    kutubxona.kitob_qoshish(k3)
    kutubxona.kitob_qoshish(k4)

    user = Foydalanuvchi("Jasur")
    vip = AVIPUser("Aziz")

    user.kitob_olish(k1)
    user.kitob_olish(k2)

    vip.kitob_olish(k3)

    print(kutubxona)

    print(
        "Kutubxonadagi kitoblar:",
        len(kutubxona)
    )

    print(
        "Bandlik foizi:",
        kutubxona.bandilik_foizi,
        "%"
    )

    print(
        "Mavjud kitoblar:"
    )

    for k in kutubxona.mavjud_kitoblar:
        print("-", k)

    print(
        "\nPython kitobi kutubxonadami?"
    )

    print(k1 in kutubxona)

    print(
        "\nBirinchi kitob:"
    )

    print(kutubxona[0])

    print(
        "\nFoydalanuvchi kitoblari:"
    )

    for k in user.olgan_kitoblar:
        print(k)
