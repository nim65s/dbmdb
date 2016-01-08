title: Django: Choices & Enum
date: 2015-08-01
author: Nim
sites: saurel.me

Choices can be efficiently represented in Django: you can use a small CharField of keys stored in the database, associated with verboses descriptions, which can even be translated.

Here is the example from [the official doc](https://docs.djangoproject.com/en/1.8/ref/models/fields/#choices):

    :::python
    from django.db import models

    class Student(models.Model):
        FRESHMAN = 'FR'
        SOPHOMORE = 'SO'
        JUNIOR = 'JR'
        SENIOR = 'SR'
        YEAR_IN_SCHOOL_CHOICES = (
            (FRESHMAN, 'Freshman'),
            (SOPHOMORE, 'Sophomore'),
            (JUNIOR, 'Junior'),
            (SENIOR, 'Senior'),
        )
        year_in_school = models.CharField(max_length=2,
                                          choices=YEAR_IN_SCHOOL_CHOICES,
                                          default=FRESHMAN)

        def is_upperclass(self):
            return self.year_in_school in (self.JUNIOR, self.SENIOR)

`year_in_school` is then really convinient to use, because you get the HTML's `<select>` that exactly fits your needs, and Django also generates for you a [`get_year_in_school_display()`](https://docs.djangoproject.com/en/1.8/ref/models/instances/#django.db.models.Model.get_FOO_display) method that outputs the verbose version you are looking for.

But, hey, who wants to write all of those constants ?
Here is my version for the same functionnalities, you'll quickly understand my point:

    :::python
    from django.db import models
    from enum import IntEnum

    def enum_to_choices(enum):
        return ((item.value, item.name) for item in list(enum))

    class Student(models.Model):
        YEARS_IN_SCHOOL = IntEnum('years_in_school', 'freshman sophomore junior senior')
        year_in_school = models.IntegerField(choices=enum_to_choices(YEARS_IN_SCHOOL),
                                             default=1)

        def is_upperclass(self):
            return self.year_in_chool >= YEARS_IN_SCHOOL.junior


I think that years in school may benefits of beeing represented with numbers, but in other use cases, you can also use `models.CharField` and `Enum`

