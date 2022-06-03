# exercicio
Questão  2:
class Cls35{
public static void main(String[] args){
for(int i = 1; i <= 100; i++) {
if(i%3 == 0){
System.out.println("FOO ");
if(i%5 == 0){
System.out.println("BAA ");
if(i%3==0 && i%5==0)){ 
System.out.println("FooBaa");
    }
   }
  }
}

----------------------------------------------------------------------
Questão 5:
select
case
when N.nota < 8 THEN 'NULL' when N.nota >- 8 THEN A.nome
end
Aluno.id, 
Aluno.valor
from Nota
N Aluno A
where
N.nota = A.ID 
and
A. valor <= N.nmax
and
A.valor >= N.min
order by N.nota desc, A.nome asc;
----------------------------------------------------------------------
Questão 8:
select distinct name from 
(
select name from cats
union all
select name from dogs
)animals
----------------------------------------------------------------------
Questão 9:




bloco anonimo
 declare 

 nr_sequencial number;
 v_data date ;

 begin 
   v_data   := to_date(trunc(sysdate));

   select sqtcall.nextval into nr_sequencial  from dual;

   procedure prc_estrutura_call;

   prc_inclui_record_omnatureza;

   insert into tcall (oid , tipo ,  subtipo ,   data_criacao) values ( nr_sequencial, tipo, subtipo, v_data );

 exception 
   when others then 
    rollback;

end;

create or replace procedure prc_estrutura_call
as 
create sequence sqtcall
    MINVALUE 1
    MAXVALUE 500
    START WITH 1
    INCREMENT BY 1
    CACHE 20;
create table tcall(
                oid number primary key,
                tipo number,
                subtipo number,
                data_criacao date
);

create table om_record(
                oid number primary key,
                tipo number,
                subtipo number,
                natureza number,
                data_criacao date
);

create table om_record_natureza(
                oid number primary key,
                tipo number,
                subtipo number,
                natureza number
);

end prc_estrutura_call;
create or replace  prc_inclui_record_omnatureza;
 as 
  begin 
  insert into om_record_natureza values (1, 1, 1, 2);
  insert into om_record_natureza values (2, 1, 2, 4);
  insert into om_record_natureza values (3, 2, 1, 6);
end ; 



create or replace procedure prc_atualiza_om_record(p_oid in number,p_tipo in number, p_sub_tipo in number )
as 
data_criacao  data_criacao.om_record%type, 
v_natureza number;
v_cont  number;
begin 
   v_cont := 0;
   data_criacao:= trunc(sysdate);
     -- O sistema deverá verificar se o campo tipo e subtipo da tabela tcall possuem relação na tabela om_record_natureza. 
  select count () into v_cont 
  from
  tcall t, om_record_natureza o
   where
   t.tipo = o.tipo
   and 
   t.subtipo = o.subtipo;
 -- não há relação entre as tabelas  tcall e om_record_natureza 
   if v_cont  = 0 then 
      v_natureza =  0;
  end if ;

  -- caso haja relação   entre as tabelas 
      if v_cont <> 0  then 
      select natureza into  v_natureza
  from  om_record_natureza
      where 
       tipo = p_tipo 
       and 
       subtipo = p_sub_tipo ,
 
   insert into om_record(oid , tipo ,  subtipo ,   natureza ,  data_criacao) values ( (p_oid , p_tipo, p_sub_tipo, v_natureza , v_data ) 
end prc_atualiza_om_record;

create or replace trigger tcall_before_insert
before insert
   on tcall
   for each row
declare

begin
             procedure prc_atualiza_om_record( :new.data_criacao  :new.tipo in number , :new.sub_tipo in number )
end;
----------------------------------------------------------------------
Questão 10:
Resposta : Será sempre 0
-----------------------------------------------------------------------
Questão 11 e 12 :

--BLOCO ANONIMO
declare

begin 
  set serveroutput on; 
   pkg_equipe_mgmt.prc_estrutura; 
   pkg_equipe_mgmt.prc_IncluiEquipe('ALPHA1','MT_07019','13TRF','E08796',0); 
   pkg_equipe_mgmt.prc_IncluiEquipe('BETA2',' MT_11606','13TRF',' E08115',1);
   pkg_equipe_mgmt.prc_IncluiEquipe('BETA1',' MT_07901', 1'3TRF',' E08115',1);
   commit;
   dbms_output.put_line('Processo processo equipe _mgmt executado com sucesso');
exception 
  when others then 
   rollback;
   raise_application_error(-20001,'Erro no processo equipe _mgmt - ' ||sqlcode|| ' -error- ' ||sqlerrm);
end ; 
-------------------------------------------------------------------------------------
--PACKAGE 
create package pkg_equipe_mgmt
as 


  procedure prc_estrutura;
  procedure  prc_IncluiEquipe(
      c_nome tb_equipe.nome%type, 
      c_nome1 tb_equipe.nome_b1%type, 
      c_nome2 tb_equipe.nome_b2%type, 
      c_nome3 tb_equipe.nome_b3%type, 
      c_status tb_equipe.status%type);   
  function fn_atualiza_tarefa(p_nome in varchar2,p_tarefa in varchar2) return varchar2;

end pkg_equipe_mgmt;  
-------------------------------------------------------------------------------------
--PACKAGE BODY
create or replace  packge body  pkg_equipe_mgmt 
as 
     procedure  prc_IncluiEquipe(c_oid  tb_equipe.oid%type, 
      c_nome tb_equipe.nome%type, 
      c_nome1 tb_equipe.nome_b1%type, 
      c_nome2 tb_equipe.nome_b2%type, 
      c_nome3 tb_equipe.nome_b3%type, 
      c_status tb_equipe.status%type) 
   is 
   begin
    insert into tb_equipe (nome,nome_b1,nome_b2,nome_b3,status)
         values(c_nome, c_nome1, c_nome2, c_nome3, c_status ); 
   end  prc_IncluiEquipe; 
procedure prc_estrutura
as 

create sequence sqEquipe
    MINVALUE 1
    MAXVALUE 300
    START WITH 1
    INCREMENT BY 1
    CACHE 20;

 create table tb_equipe(
    oid NUMBER ,
    nome VARCHAR2(50) ,
    nome_b1 VARCHAR2(50) ,
    nome_b2 VARCHAR2(50) ,
    nome_b3 VARCHAR2(50) ,
    status number(1)
);
 create table  tb_equipe_tarefa(
    oid NUMBER ,
    nome VARCHAR2(50) ,
    data_criacao date ,
    area VARCHAR2(100) ,
    equipe_responsavel number
);
  create table  tb_equipe_log(
    oid NUMBER ,
    data date ,
    codigo VARCHAR2(50) ,
    descricao VARCHAR2(100) ,
    equipe_responsavel number
);
end prc_estrutura;

function fn_atualiza_tarefa(p_nome in varchar2,p_tarefa in varchar2)     
  return   number is
   nr_sequencial                    tb_equipe.oid%type,
   v_equipe_responsavel      tb_equipe_tarefa. equipe_responsavel%type,
   v_area                              varchar2(150);
   v_oid                                 tb_equipe.oid%type,
   v_msg_ret                          char;
   v_status                             tb_equipe.status%type,
   v_data                                tb_equipe.data_criacao%type,
begin
   v_oid := 0;
   v_msg_ret  := 0;
   v_status :=  0;
   v_data := to_date(trunc(sysdate));

   select sqEquipe.nextval into nr_sequencial  from dual;

   select nvl(nome_b1,'')||'/'||nvl(nome_b2,'')||'/'||nvl(nome_b3,'') into v_area from tb_equipe;

   select 
    e.oid, e.status into v_oid,v_status 
   from tb_equipe e
   where e.nome <> p_nome
   and exists 
   (select 1 from tb_equipe q
     where q.nome_b1 = e.nome_b1
     and     q.nome_b2 = e.nome_b2
     and     q.nome_b3 = e.nome_b3
     and     q.nome = p_nome );
   -- caso exista a equipe 
   if  nvl(v_oid, 0) <> 0 then
       v_equipe_responsave := v_oid;
       v_msg_ret  := 0;
   end if ;
    -- caso não exista a equipe
   if  nvl(v_oid, 0) = 0 then
       v_equipe_responsavel := 0;
       v_msg_ret  := -1;
   end if ;
   -- caso exista a equipe mas esta inativa
   if nvl(v_status, 0) = 0 then
       v_equipe_responsave := 0;
       v_msg_ret  := -2;
   end if;
   update  tb_equipe set oid = nr_sequencial where nome = pnome ;
   insert into tb_equipe_tarefa (oid,nome,data_criacao,area,equipe_responsavel)values (nr_sequencial, p_nome,v_data,v_area,    v_equipe_responsavel )
   insert into tb_equipe_log(oid,data,codigo,descricao,equipe_responsavel)values (nr_sequencial, v_data,'codigo','descrição', v_equipe_responsavel  )
    return 0 ;

when no_data_found then 
v_msg_ret  := -2;

   insert into tb_equipe_tarefa (oid,nome,data_criacao,area,equipe_responsavel)values (nr_sequencial, p_nome,v_data,v_area,      v_equipe_responsavel )
   insert into tb_equipe_log(oid,data,codigo,descricao,equipe_responsavel)values (nr_sequencial, v_data )
    return 0 ;

 when others then 
   raise_application_error(-20001,'Erro no processo equipe _mgmt - '||SQLCODE||' -ERROR- '||SQLERRM);

end fn_atualiza_tarefa;

end pkg_equipe_mgmt;


