1.
bee generate docs
2.
bee run watchall true



http://127.0.0.1:4000/swagger/




// @Title RoleSortUpdate
// @Description 角色排序更新
// @Param	roleIdsSortJson				body 	jsonArray		true		[{roleId:1,sort:1},{roleId:2,sort:3},{roleId:3,sort:2}]
// @Success 200 角色排序更新成功
// @Failure 12155 角色排序更新失败
// @router /roleSortUpdate [post]
func (roleC *RoleController) RoleSortUpdate() {

}
