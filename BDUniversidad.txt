--BASE DE DATOS
--AUTOR: ARTEAGA LOPEZ ABRAHAM
--09/08/2022

use master
go

if DB_ID('BDUniversidad') is not null
	drop database BdUniversidad
go
create database BDUniversidad
go

use BDUniversidad
go

--Creacion de Tablas
if OBJECT_ID('TEscuela') is not null
	drop table TEscuela
go
create table TEscuela
(
	CodEscuela char(3) primary key,
	Escuela varchar(50),
	Facultad varchar(50)
)
go

if OBJECT_ID('TAlumno') is not null
	drop table TAlumno
go
create table TAlumno
(
	CodAlumno char(5) primary key,
	Apellidos varchar(50),
	Nombres varchar(50),
	LugarNac varchar(50),
	FechaNac datetime,
	CodEscuela char(3),
	foreign key (CodEscuela) references TEscuela
)
go

--Insertamos los datos
insert into TEscuela values('E01','Sistemas','Ingenieria')
insert into TEscuela values('E02','Civil','Ingenieria')
insert into TEscuela values('E03','Industrial','Ingenieria')
insert into TEscuela values('E04','Ambiental','Ingenieria')
insert into TEscuela values('E05','Arquitectura','Ingenieria')
go

insert into TAlumno values('A01','Huaman','Marcelo','Cusco','1997-04-10','E01')
insert into TAlumno values('A02','Quispe','Marco','Quillabamba','1998-09-15','E04')
insert into TAlumno values('A03','Pacheco','Jose','Cusco','1998-08-20','E03')
insert into TAlumno values('A04','Delgado','Hiro','Cusco','2000-05-05','E02')
insert into TAlumno values('A05','Lopez','William','Quillabamba','2000-01-16','E05')
go


--PA para TEscuela
use BDUniversidad
go

--Listar
if OBJECT_ID('spListarEscuela') is not null
	drop proc spListarEscuela
go
create proc spListarEscuela
as
begin
	select CodEscuela, Escuela, Facultad from TEscuela
end
go

exec spListarEscuela

--Agregar
if OBJECT_ID('spAgregarEscuela') is not null
	drop proc spAgregarEscuela
go

create proc spAgregarEscuela
	@CodEscuela char(3),
	@Escuela varchar(50),
	@Facultad varchar(50)
as
begin
--CodEscuela no puede ser duplicado
	if not exists(select CodEscuela from TEscuela where CodEscuela=@CodEscuela)
		--Escuela no puede ser duplicado
		if not exists(select Escuela from TEscuela where Escuela=@Escuela)
			begin
				insert into TEscuela values(@CodEscuela, @Escuela, @Facultad)
				select CodError=0, Mensaje='Se inserto correctamente escuela'
			end
		else select CodError=1, Mensaje='Error: Escuela duplicada'
	else select CodError=1, Mensaje='Error: CodEscuela duplicado'
end
go

exec spAgregarEscuela 'E06','Psicologia','Medicina Humana'
exec spAgregarEscuela 'E07','Enfermeria','Medicina Humana'
exec spAgregarEscuela 'E08','Psiquiatra','Medicina Humana'
exec spListarEscuela
--Eliminar
if OBJECT_ID('spEliminarEscuela') is not null
	drop proc spEliminarEscuela
go

create proc spEliminarEscuela
	@CodEscuela char(3)
as
begin
	if exists(select * from TEscuela where CodEscuela=@CodEscuela)
		begin
			delete from TEscuela where CodEscuela=@CodEscuela
			select CodError=0, Mensaje='Se elimino correctamente escuela'
		end
	else select CodError=1, Mensaje='Error: Escuela no existe'
end
go

exec spEliminarEscuela 'E08'
exec spListarEscuela

--Actualizar
if OBJECT_ID('spActualizarEscuela') is not null
	drop proc spActualizarEscuela
go

create proc spActualizarEscuela
	@CodEscuela char(3),
	@Escuela varchar(50),
	@Facultad varchar(50)
as
begin
	if exists(select CodEscuela from TEscuela where CodEscuela=@CodEscuela)
			if not exists(select Escuela from TEscuela where Escuela=@Escuela)
				begin
					update TEscuela Set Escuela=@Escuela, Facultad=@Facultad where CodEscuela=@CodEscuela
					select CodError=0, Mensaje='Se actualizo correctamente escuela'
				end
			else select CodError=1, Mensaje='Error: Escuela duplicada'
	else select CodError=1, Mensaje='Error: CodEscuela duplicada'
end
go
exec spActualizarEscuela 'E07','Pediatra','Medicina Humana'
exec spListarEscuela

--Buscar
if OBJECT_ID('spBuscarEscuela') is not null
	drop proc spBuscarEscuela
go

create proc spBuscarEscuela
	@CodEscuela char(3)
as
begin
	if exists(select CodEscuela from TEscuela where CodEscuela=@CodEscuela)
		begin
			select * from TEscuela where CodEscuela=@CodEscuela
			select CodError=0, Mensaje='Se encontro correctamente escuela'
		end
	else select CodError=1, Mensaje='Error: Escuela no existe'
end
go

exec spBuscarEscuela 'E07'

exec spListarEscuela

--select * from TAlumno
--select * from TEscuela

--exec spEliminarEscuela 'E06'
--exec spActualizarEscuela 'E05','Arquitectura','Ingenieria'
--exec spBuscarEscuela 'E05'
--exec spAgregarEscuela 'E06','Psicologia','Medicina'
--exec spListarEscuela


--PA para TAlumno
use BDUniversidad
go

--Listar
if OBJECT_ID('spListarAlumno') is not null
	drop proc spListarAlumno
go
create proc spListarAlumno
as
begin
	select CodAlumno, Apellidos, Nombres, LugarNac, FechaNac, CodEscuela from TAlumno
end
go

exec spListarAlumno
go

--Agregar
if OBJECT_ID('spAgregarAlumno') is not null
	drop proc spAgregarAlumno
go

create proc spAgregarAlumno
	@CodAlumno char(5),
	@Apellidos varchar(50),
	@Nombres varchar(50),
	@LugarNac varchar(50),
	@FechaNac datetime,
	@CodEscuela char(3)
as
begin
--CodEscuela no puede ser duplicado
	if not exists(select CodAlumno from TAlumno where CodAlumno=@CodAlumno)
		--Escuela no puede ser duplicado
		if not exists(select Apellidos from TAlumno where Apellidos=@Apellidos)
			begin
				insert into TAlumno values(@CodAlumno, @Apellidos, @Nombres, @LugarNac, @FechaNac, @CodEscuela)
				select CodError=0, Mensaje='Se inserto correctamente alumno'
			end
		else select CodError=1, Mensaje='Error: Apellidos duplicada'
	else select CodError=1, Mensaje='Error: CodAlumno duplicado'
end
go

exec spAgregarAlumno 'A06','Arriaga','Mateo','Cusco','1997-10-08','E06'
exec spAgregarAlumno 'A07','Aliaga','Marcus','Cusco','1995-04-05','E07'
exec spListarAlumno

--Eliminar
if OBJECT_ID('spEliminarAlumno') is not null
	drop proc spEliminarAlumno
go

create proc spEliminarAlumno
	@CodAlumno char(5)
as
begin
	if exists(select * from TAlumno where CodAlumno=@CodAlumno)
		begin
			delete from TAlumno where CodAlumno=@CodAlumno
			select CodError=0, Mensaje='Se elimino correctamente alumno'
		end
	else select CodError=1, Mensaje='Error: Alumno no existe'
end
go

exec spEliminarAlumno 'A07'
exec spListarAlumno

--Actualizar
if OBJECT_ID('spActualizarAlumno') is not null
	drop proc spActualizarAlumno
go

create proc spActualizarAlumno
	@CodAlumno char(5),
	@Apellidos varchar(50),
	@Nombres varchar(50),
	@LugarNac varchar(50),
	@FechaNac datetime,
	@CodEscuela char(3)
as
begin
	if exists(select CodAlumno from TAlumno where CodEscuela=@CodEscuela)
			if not exists(select Apellidos from TAlumno where Apellidos=@Apellidos)
				begin
					update TAlumno Set Apellidos=@Apellidos, Nombres=@Nombres, LugarNac=@LugarNac, FechaNac=@FechaNac, CodEscuela=@CodEscuela where CodAlumno=@CodAlumno
					select CodError=0, Mensaje='Se actualizo correctamente alumno'
				end
			else select CodError=1, Mensaje='Error: Apellidos duplicada'
	else select CodError=1, Mensaje='Error: CodAlumno duplicada'
end
go

exec spActualizarAlumno 'A06','Cusi','Pool','Cusco','1995-04-05','E06'
exec spListarAlumno

--Buscar
if OBJECT_ID('spBuscarAlumno') is not null
	drop proc spBuscarAlumno
go

create proc spBuscarAlumno
	@CodAlumno char(5)
as
begin
	if exists(select CodEscuela from TAlumno where CodAlumno=@CodAlumno)
		begin
			select * from TAlumno where CodAlumno=@CodAlumno
			select CodError=0, Mensaje='Se encontro correctamente alumno'
		end
	else select CodError=1, Mensaje='Error: Alumno no existe'
end
go

exec spBuscarAlumno 'A06'

exec spListarAlumno

