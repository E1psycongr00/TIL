# 20220119(MyBatis동적SQL)

작성일시: 2022년 1월 19일 오후 10:19

# 오늘 배운것

- MyBatis에서 강력한 기능 중 하나가 동적 SQL을 지원하는 것이다.
    - 동적 SQL을 사용하는 이유는 들어가는 변수에 따라 if, case, for구문 반복을 써야 할 때가 있는 것이다. 예를 들면 parameterType으로 array나 list가 들어간다면 바인드 변수도 array 길이에 따라 바뀔 수 밖에 없는데 이를 직접 구현하기는 어렵다.

- IF
    - Where에 일부로 포함해서 사용 할 수 있다.
    
    ```sql
    <select id="findActiveBlogWithTitleLike"
         resultType="Blog">
      SELECT * FROM BLOG
      WHERE state = ‘ACTIVE’
      <if test="title != null">
        AND title like #{title}
      </if>
    </select>
    
    ```
    
    이 코드를 해석해보면 블로그 테이블의 모든 컬럼을 가져와서 보여줘라 근데 state = ‘active’인 컬럼을 보여주고 만약 title 값이 존재한다면 and title like #{title} 구문도 실행해라.
    
    ```sql
    <if test="조건식"> SQL 코드 </if>
    ```
    
    조건식이 참이면 SQL 코드를 실행해라.
    

- **where** 과 **set**
    - mybatis에서는 <where> </where> <set> </set> 을 지원한다.
        - <if></if> 문을 쓸 경우 조건이 만족 하지 않으면 안에 해당 sql문을 실행하지 않는다. 만약 where, set절에 모두 if 문으로 써져 있다면 모두 만족 하지 않았을 때  where set만 달랑 존재하게 되어 SQL 구문 에러를 뱉을 것이다. 그래서 동적으로 where,set을 지원한다.
        
        ```sql
        <update id="updateAuthorIfNecessary">
          update Author
            <set>
              <if test="username != null">username=#{username},</if>
              <if test="password != null">password=#{password},</if>
              <if test="email != null">email=#{email},</if>
              <if test="bio != null">bio=#{bio}</if>
            </set>
          where id=#{id}
        </update>
        ```
        

- **foreach**
    - foreach는 동적 SQL 작성에 있어서 가장 중요한 구문이다. 동적인 반복처리가 SQL에서는 힘들다. 내 생각에는 불가능하다. 그러나 자바에서 입력 특성상 여러개의 입력이 크기가 무작위로 들어 갈 수 있다. 이때 foreach는 강력한 위력을 발휘한다. 주로 in (?, ?, ?) 절에 많이 쓴다.
    
    ```sql
    <foreach item="item" index="index" collection="list"
            open="ID in (" separator="," close=")" nullable="true">
              #{item}
    </foreach>
    ```
    
    해석해보면 item이라는 네이밍을 가지고 list 타입을 입력으로 받을 때 
    
    ID in (#{item}, #{item}, #{item}, #{item} .......) 과 같은 형태의 sql 구문을 생성한다.
    
    ```sql
    <select id="selectPostIn" resultType="domain.blog.Post">
      SELECT *
      FROM POST P
      <where>
        <foreach item="item" index="index" collection="list"
            open="ID in (" separator="," close=")" nullable="true">
              #{item}
        </foreach>
      </where>
    </select>
    ```
    

# 오늘 느낀점

- 동적 SQL을 처음 배울때 이게 뭐지...? 하는 생각이 들고 어려웠는데 자료를 정리하면서 다시 살펴보니 굉장히 간단한 개념이였다. SQL의 한계를 극복하기 위해 나름의 식별자와 규칙으로 다양한 조건 또는 길이의 입력이 주어졌을 때 쉽게 처리 할 수 있다.